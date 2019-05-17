About the token library API
-------------------------------

The API exposed by the library can be classified in three main parts:
  * Common token abstractions, including secure channels initialization and common instructions, as well as callbacks to handle Pet Pin, Pet Name and User Pin
  * AUTH token specific instructions
  * DFU token specific instructions

.. note::
  The token library heavily relies on the libecc external dependency for the public key
  cryptography (mainly ECDSA and ECDH), and on libaes and libhash for the symmetric
  cryptography

Generic token API
"""""""""""""""""""""""""

The generic token API is exposed in the ``libtoken.h`` public header. The API can be divided
in three types of functions:

  * Functions handling secure token initialization, including secure channel establishment
  * Functions handling token instructions
  * Functions handling callbacks with user interactions (i.e. managing Pet Pin, Pet Name and User Pin through a user interface)

Token initialization is performed through the following APIs: ::

  int token_early_init(token_map_mode_t token_map);
  int token_init(token_channel *channel);

The early init function is called before the nominal phase, and during the nominal phase
a communication wit an inserted token can be established using ``token_init``, resulting in
mounting a ``channel`` with it.

.. danger::
  Using ``token_init`` establishes a non secure channel with a generic token! Secure communications
  with the token are NOT ensured using only this API: the user MUST use ``token_secure_channel_init``
  to establish a secure channel

When the channel with the token is established, ``token_secure_channel_init`` must be called to
establish a secure channel: ::
  
  int token_secure_channel_init(token_channel *channel, const unsigned char *decrypted_platform_priv_key_data, uint32_t decrypted_platform_priv_key_data_len, const unsigned char *decrypted_platform_pub_key_data, uint32_t decrypted_platform_pub_key_data_len, const unsigned char *decrypted_token_pub_key_data, uint32_t decrypted_token_pub_key_data_len, ec_curve_type curve_type, unsigned int *remaining_tries);

This function has the following API:
  
  * ``token_channel \*channel``: this is a pointer to an abstract structure representing an established (non secure) channel initialized with ``token_init``
  * ``const unsigned char \*decrypted_platform_priv_key_data``: this is a pointer to decrypted ECDSA platform private key, and ``uint32_t decrypted_platform_priv_key_data_len`` is its size
  * ``const unsigned char \*decrypted_platform_pub_key_data``: this is a pointer to decrypted ECDSA platform public key, and  ``uint32_t decrypted_platform_pub_key_data_len`` is its size
  * ``const unsigned char \*decrypted_token_pub_key_data``: this is a pointer to decrypted ECDSA token public key, and ``uint32_t decrypted_token_pub_key_data_len`` is its size
  * ``ec_curve_type curve_type``: this is the curve type to use when performing ECDSA and ECDH (three curves are currently supported  in the WooKey project: BRAINPOOLP256R1, SECP256R1 and FRP256V1)
  * ``unsigned int \*remaining_tries``: this variable holds (as an output of the function) the number of remaining tries to establish the secure channel with the token

It is possible to communicate with a token over a channel (secure or not) using the following API: ::

  int token_send_receive(token_channel *channel, SC_APDU_cmd *apdu, SC_APDU_resp *resp);

This functions allows to send APDUs and get responses (in the ISO7816 sense) over the initialized channel ``channel``.
The low layers handle the fact that the channel is secure or not, encrypting the commands or not depending on the
state of the channel. 

.. danger::
  Beware when using ``token_send_receive`` with sensitive commands: you always want to check if a channel is secure
  when sending confidential data to the token. This can be done by checking the ``channel->secure_channel`` that must
  be equal to 1

Generic token instructions are compiled in the following enumeration: ::

  /* Our common token instructions */
  enum token_instructions {
        TOKEN_INS_SELECT_APPLET = 0xA4,
        TOKEN_INS_SECURE_CHANNEL_INIT = 0x00,
        TOKEN_INS_UNLOCK_PET_PIN = 0x01,
        TOKEN_INS_UNLOCK_USER_PIN = 0x02,
        TOKEN_INS_SET_USER_PIN = 0x03,
        TOKEN_INS_SET_PET_PIN = 0x04,
        TOKEN_INS_SET_PET_NAME = 0x05,
        TOKEN_INS_USER_PIN_LOCK = 0x06,
        TOKEN_INS_FULL_LOCK = 0x07,
        TOKEN_INS_GET_PET_NAME = 0x08,
        TOKEN_INS_GET_RANDOM = 0x09,
        TOKEN_INS_DERIVE_LOCAL_PET_KEY = 0x0a,
  };

Each of these instructions is associated with an API function. ``TOKEN_INS_SELECT_APPLET``, ``TOKEN_INS_DERIVE_LOCAL_PET_KEY`` and
``TOKEN_INS_SECURE_CHANNEL_INIT`` are sent over a non secure channel (obviously since they help at establishing such a secure channel).
All the other instructions must be sent over an established secure channel, or they will return an error.

All the function return 0 on success, and non zero on error.

The ``TOKEN_INS_SELECT_APPLET`` selects a Javacard applet according to its AID (Applet ID): ::

  int token_select_applet(token_channel *channel, const unsigned char *aid, unsigned int aid_len);

The ``TOKEN_INS_DERIVE_LOCAL_PET_KEY`` is executed by the function: ::

  int decrypt_platform_keys(token_channel *channel, const char *pet_pin, uint32_t pet_pin_len, const databag *keybag, uint32_t keybag_num, databag *decrypted_keybag, uint32_t decrypted_keybag_num, uint32_t pbkdf2_iterations)

This routine uses the Pet Pin to derive, using the token, a key to decrypt the local keys stored in the platform flash. This complex cryptographic scheme
has been designed to thwart offline and online brute force attacks on the Pet Pin.

The ``TOKEN_INS_SECURE_CHANNEL_INIT`` establishes a secure channel, and is related to: ::
  
  int token_secure_channel_init(token_channel *channel, const unsigned char *decrypted_platform_priv_key_data, uint32_t decrypted_platform_priv_key_data_len, const unsigned char *decrypted_platform_pub_key_data, uint32_t decrypted_platform_pub_key_data_len, const unsigned char *decrypted_token_pub_key_data, uint32_t decrypted_token_pub_key_data_len, ec_curve_type curve_type, unsigned int *remaining_tries);

The ``TOKEN_INS_UNLOCK_PET_PIN`` and ``TOKEN_INS_UNLOCK_USER_PIN`` are sent using a unique function: ::

  int token_send_pin(token_channel *channel, const char *pin, unsigned int pin_len, unsigned char *pin_ok, unsigned int *remaining_tries, token_pin_types pin_type);

where the ``pin_type`` is either ``TOKEN_PET_PIN`` or ``TOKEN_USER_PIN``. The ``remaining_tries`` are returned by the instructions, as well as ``pin_ok`` that is 0 on failure and non zero on success.

The ``TOKEN_INS_SET_USER_PIN`` and ``TOKEN_INS_SET_PET_PIN`` are handled by the following function: ::
  
  int token_change_pin(token_channel *channel, const char *pin, unsigned int pin_len, token_pin_types pin_type);

They allow to modify the Pin values in the token and suppose that the user is already authenticated with his two pins.

The ``TOKEN_INS_GET_PET_NAME`` is executed by: ::

  int token_get_pet_name(token_channel *channel, char *pet_name, unsigned int *pet_name_length);

and allows to get the Pet Name whenever the Pet Pin has been presented to the token.

The ``TOKEN_INS_SET_PET_NAME``: ::
  
   int token_set_pet_name(token_channel *channel, const char *pet_name, unsigned int pet_name_length);

sets a new Pet Name, and supposes that the user is fully authenticated (through Pet Pin and User Pin).

``TOKEN_INS_USER_PIN_LOCK`` is related to: ::

  int token_user_pin_lock(token_channel *channel);

It locks the token with regards to the User Pin but not to the Pet Pin, and the secure channel is kept opened.

``TOKEN_INS_FULL_LOCK`` is related to: ::

  int token_full_lock(token_channel *channel);

and fully locks the token, i.e. Pet Pin and User Pin are considered as non authenticated, and the secure channel
is closed.

The ``TOKEN_INS_GET_RANDOM`` is implemented by: ::

  int token_get_random(token_channel *channel, char *random, uint8_t random_len);

This instruction asks of ``random_len`` bytes of random data to put in ``char *random``. This can be useful since the tokens are
secure elements with clean entropy sources.

A generic function allows to abstract all the interactions with the token while using callbacks (see below) to interact with the
user: ::

  int token_unlock_ops_exec(token_channel *channel, const unsigned char *applet_AID, unsigned int applet_AID_len, const databag *keybag, uint32_t keybag_num, uint32_t pbkdf2_iterations, ec_curve_type curve_type, token_unlock_operations *op, uint32_t num_ops, cb_token_callbacks *callbacks, unsigned char *sig_pub_key, unsigned int *sig_pub_key_len, databag *saved_decrypted_keybag, uint32_t saved_decrypted_keybag_num);

The possible operations allowed by ``token_unlock_ops_exec`` are: ::

  typedef enum {
        TOKEN_UNLOCK_INIT_TOKEN               = 0,
        TOKEN_UNLOCK_ESTABLISH_SECURE_CHANNEL = 1,
        TOKEN_UNLOCK_ASK_PET_PIN              = 2,
        TOKEN_UNLOCK_PRESENT_PET_PIN          = 3,
        TOKEN_UNLOCK_ASK_USER_PIN             = 4,
        TOKEN_UNLOCK_PRESENT_USER_PIN         = 5,
        TOKEN_UNLOCK_CONFIRM_PET_NAME         = 6,
        TOKEN_UNLOCK_CHANGE_PET_PIN           = 7,
        TOKEN_UNLOCK_CHANGE_USER_PIN          = 8,
        TOKEN_UNLOCK_CHANGE_PET_NAME          = 9,
  } token_unlock_operations;



Token user callbacks
""""""""""""""""""""

In order to make interactions with the user easier while keeping an abstraction with the way these interactions are performed
(this can be a GUI, an UART communication, etc.), the token library makes use of callbacks.

Callbacks are function pointers defined as follows: ::

  typedef int (*cb_token_request_pin_t)(char *pin, unsigned int *pin_len, token_pin_types pin_type, token_pin_actions action);
  typedef int (*cb_token_acknowledge_pin_t)(token_ack_state ack, token_pin_types pin_type, token_pin_actions action, uint32_t remaining_tries);
  typedef int (*cb_token_request_pet_name_t)(char *pet_name, unsigned int *pet_name_len);
  typedef int (*cb_token_request_pet_name_confirmation_t)(const char *pet_name, unsigned int pet_name_len);

  typedef struct {
        cb_token_request_pin_t                   request_pin;
        cb_token_acknowledge_pin_t               acknowledge_pin;
        cb_token_request_pet_name_t              request_pet_name;
        cb_token_request_pet_name_confirmation_t request_pet_name_confirmation;
  } cb_token_callbacks;

They are split in four interaction types:

   * ``cb_token_request_pin_t``: this is the callback to request a pin (either Pet Pin or User Pin) from the user, either for an authentication or for a Pin modification
   * ``cb_token_acknowledge_pin_t``: this is the callback used to send the user a confirmation (or no confirmation) that his Pin is correct and has been validated by the token
   * ``cb_token_request_pet_name_t``: this allows to request the Pet Name when modifying the Pet Name in the token
   * ``cb_token_request_pet_name_confirmation_t``: this asks the user for a confirmation that the Pet Name returned by the token is indeed OK or not

AUTH token instructions
"""""""""""""""""""""""""
The AUTH token implements a specific instruction exposed by the ``libtoken_auth.h`` header: ::

  /* Our authentication token specific instructions */
  enum auth_token_instructions {
        /* This handles the encryption master key in AUTH mode */
        TOKEN_INS_GET_KEY = 0x10,
  };

This instruction is implemented through: ::

  int auth_token_get_key(token_channel *channel, const char *pin, unsigned int pin_len, unsigned char *key, unsigned int key_len, unsigned char *h_key, unsigned int h_key_len);


This function asks for the master encryption key and puts it in the ``unsigned char *key`` if successful. The ``unsigned char *h_key`` (the hash of the key) is
also filled as it is needed for the AES-CBC-ESSIV algorithm (for sectors IV derivation). Finally, ``const char *pin`` is an
input and is needed since the token encrypts sensitive values with a key derived from the User Pin (this encryption
is an additional confidential layer over the secure channel).


DFU token instructions
"""""""""""""""""""""""""

The DFU token supports the following specific instructions: ::

  /* Our DFU token specific instructions */
  enum dfu_token_instructions {
        /* This handles firmware encryption key derivation in DFU mode */
        TOKEN_INS_BEGIN_DECRYPT_SESSION = 0x20,
        TOKEN_INS_DERIVE_KEY = 0x21,
  };

The ``TOKEN_INS_BEGIN_DECRYPT_SESSION`` is implemented in: ::

  int dfu_token_begin_decrypt_session(token_channel *channel, const unsigned char *header, uint32_t header_len);

This function opens a firmware decrypt session by sending the firmware header. This header is checked
by the token, and an error is returned if the header authenticity is not correct.

The ``TOKEN_INS_DERIVE_KEY`` is executed by: ::

  int dfu_token_derive_key(token_channel *channel, unsigned char *derived_key, uint32_t derived_key_len, uint16_t num_chunk);

This function asks the DFU token for chunk keys derivation. The chunk number is ``uint16_t num_chunk``, and the derived key is
received in the ``unsigned char *derived_key`` buffer. This derivation can only take place if a decrypt
session has been properly opened with ``dfu_token_begin_decrypt_session``.
