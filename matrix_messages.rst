#########################
Raiden transport messages
#########################

Definitions
===========
* Sender: entity sending the message
* Recipient: entity able to receive and decode the message
* Message: A data object exchanged between Sender and Recipient
* Message Field: an attribute of JSON data format that is required to be included in the message

Transport envelope
==================
Messages are exchanged in JSON format.
Message itself contains metadata that allow recipient to verify sender of the message (via use of PK signatures).

Rationale
---------
* plaintext JSON messages are easy to read for both humans and machines
* JSON format is widely used and can be easily validated using JSON-schema (http://json-schema.org/)
* JSON is easily extensible and extension can be made backward-compatible
* Message format must not be dependent on the transport used - it should not matter whether the message is sent via Matrix or REST
* Sender identity verification must not be part of the transport. It should be possible to submit the message over an untrusted channel or delegate.

Example of a message
-------------------
``
{
    'signature': '0xabcd....01234',
    'content-type’: 'base-64',
    'data': 'eyAnaGVhZGVyJzogeyAndHlwZScgOiAndGV4dC1tZXNzYWdlJywgJ3NlbmRlcic6ICAnMHhhYWVlLi4uMTIzNCcsICd0aW1lc3RhbXAnOiAxMjM0NTY3ODkgfSwNCidib2R5JzogeyAnbWVzc2FnZSc6ICdoZWxsbyB3b3JsZCcgfX0='
}
``

Envelope fields:
===============
* signature - a sender signature of the data field
* content-type - how the data are encoded, i.e.:
    * text-plain: a simple escaped JSON stored as a JSON string
    * base-64: base-64 encoded JSON
* data - transmitted data
Message MUST contain all mentioned fields and MUST be discarded by the Recipient otherwise.

After unpacking `data` using the method(s) specified in content-type field, recipient MUST obtain a JSON object of the following format:
``
{
    'header': {
        'sender': '',
        'timestamp': '',
        'type': ''
    }
    'body': { <JSON OBJECT> }
}
``

Header fields
=============
* Sender (string) - public key used to verify signature of a message. Recipient MUST validate the signature and MUST discard the message if the signature doesn’t match
* Timestamp (int) - time the message was sent (note: a block hash might also be used to enforce timestamp tampering)
* Type (string) - user-specified type of the message

Body field MUST contain a valid JSON object, but the contents are unspecified.



Appendix A: Balance proof example
=================================

``
{
    'header': {
        'sender': '0x7f6a705c403bdf87e74109f1a715e560e0a63753',
        'timestamp': '1521019660',
        'type': 'BalanceProof'
    }
    'body': { 
        'balance_proof': {
            'nonce': 1,
            'transferred_amount': 90,
            'locksroot': '0x0000000000000000000000000000000000000000000000000000000000000000',
            'extra_hash': '0x647d722644d0dc43da475586ec9150e1d26ac0aceedb3b642d50c0b023d8774e',
            'signature': '0x6ef31b0aa19e093721419d8e0eabe11e67824a8daa3549c99e7fa0992d43de022181cefe0fe0c91855bca40f1a73287ec95e690d59e64ce9ebd436cf834bbd7f1b'
        }
    }
}
``


See also `Messaging SPEC
<https://github.com/raiden-network/spec/blob/master/messaging.rst>`_.


