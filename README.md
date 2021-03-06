Chain-PHP
========

[![Build Status](https://travis-ci.org/Digital-Currency-Research/Chain-PHP.svg)](https://travis-ci.org/Digital-Currency-Research/Chain-PHP)
[![Code Climate](https://codeclimate.com/github/Digital-Currency-Research/Chain-PHP/badges/gpa.svg)](https://codeclimate.com/github/Digital-Currency-Research/Chain-PHP)
[![Test Coverage](https://codeclimate.com/github/Digital-Currency-Research/Chain-PHP/badges/coverage.svg)](https://codeclimate.com/github/Digital-Currency-Research/Chain-PHP)

This library provides a simple PHP interface to the [Chain Bitcoin API](https://chain.com/).

[All methods](https://chain.com/docs) are supported including sending transactions and creating webhooks and webhook events. You will need to [sign up to Chain](https://chain.com/) for a free API Key and Key Secret as they are required to authenticate requests.

### Installing via Composer

The recommended way to install the library is through [Composer](http://getcomposer.org).

```bash
# Install Composer
curl -sS https://getcomposer.org/installer | php

# Add Chain-PHP as a dependency
php composer.phar require cbix/chain-php:dev-master
```

After installing, you need to require Composer's autoloader:

```php
require 'vendor/autoload.php';
```

### Setup

You will need to reference your API Key ID and API Key Secret as provided by Chain to authenticate your requests. There is an optional third parameter, which if set to true will use the Bitcoin Testnet3 block chain.
It defaults to using the Bitcoin Mainnet if not specified or set to false.

    $chain = Chain::make($key, $secret, false);

### Return Values

Please see the [Chain documentation](https://chain.com/docs/v1/) for the various return objects for each method call.

* [Bitcoin Address Object](https://chain.com/docs#bitcoin-address-transactions)
* [Bitcoin Transaction Object](https://chain.com/docs#bitcoin-address-transactions)
* Output Object array
* OP_RETURN Object.
* [Webhook URL Object](https://chain.com/docs/#object-webhooks)

### Address

Further details are available in the examples included. Where the API accepts multiple addresses you should provide an array of addresses.

    $single_address = $chain->get_address('17x23dNjXJLzGMev6R63uyRhMWP1VHawKc');
    $multiple_addresses = $chain->get_address(['17x23dNjXJLzGMev6R63uyRhMWP1VHawKc', '1EX1E9n3bPA1zGKDV5iHY2MnM7n5tDfnfH']);
    $transactions = $chain->get_address_transactions('17x23dNjXJLzGMev6R63uyRhMWP1VHawKc');
    $unspents = $chain->get_address_unspents('17x23dNjXJLzGMev6R63uyRhMWP1VHawKc');
    $op_returns = $chain->get_address_op_returns('17x23dNjXJLzGMev6R63uyRhMWP1VHawKc');

### Transaction

You may send a transaction to the blockchain but you will need to determine the appropriate hex representation of the signed transaction.
In order to achieve this please consider using the [bitcoin-lib-php library](https://github.com/Bit-Wasp/bitcoin-lib-php).

    $transaction = $chain->get_transaction('0f40015ddbb8a05e26bbacfb70b6074daa1990b813ba9bc70b7ac5b0b6ee2c45');
    $op_return = $chain->get_transaction_op_return('0f40015ddbb8a05e26bbacfb70b6074daa1990b813ba9bc70b7ac5b0b6ee2c45');
    $confidence = $chain->get_transaction_confidence('0f40015ddbb8a05e26bbacfb70b6074daa1990b813ba9bc70b7ac5b0b6ee2c45');
    $send = $chain->send_transaction('0100000001ec...');

### Block

The get_block and get_block_op_returns will accept either a block hash or block height as an input.

    $block = $chain->get_block('00000000000000009cc33fe219537756a68ee5433d593034b6dc200b34aa35fa');
    $latest_block = $chain->get_latest_block();
    $block_op_returns = $chain->get_block_op_returns('00000000000000009cc33fe219537756a68ee5433d593034b6dc200b34aa35fa');

## Webhooks

### Setup

For webhooks you will need to create a webhook client passing your API Key and API Key Secret.

    $webhook = Chain::webhook($key, $secret);

When creating webhooks the id attribute may be specified or if not provided will be generated by the Chain API.

    $create_webhook = $webhook->create_webhook('https://username:password@your-server-url.com', 'FFA21991-5669-4728-8C83-74DEC4C93A4A');
    $list_webhooks = $webhook->list_webhooks();
    $update_webhook = $webhook->update_webhook('FFA21991-5669-4728-8C83-74DEC4C93A4A', 'https://username:password@your-server-url.com');
    $delete_webhook = $webhook->delete_webhook('FFA21991-5669-4728-8C83-74DEC4C93A4A');

    $options = [
        'event' => 'address-transaction',
        'block_chain' => 'bitcoin',
        'address' => '1...',
        'confirmations' => 1
    ];
    $create_webhook_event = $webhook->create_webhook_event('FFA21991-5669-4728-8C83-74DEC4C93A4A', $options);

    $list_webhook_events = $webhook->list_webhook_events('FFA21991-5669-4728-8C83-74DEC4C93A4A');
    $delete_webhook_event = $webhook->delete_webhook_event('FFA21991-5669-4728-8C83-74DEC4C93A4A');

## Exceptions

If there are any issues during the API request a ChainException or ChainWebhookException will be thrown which can be caught
and managed according to your application needs.

    try {
        $latest_block = $chain->get_latest_block();
        echo $latest_block->block_id;
    } catch (ChainException $e) {
        //There was an error more information in $e->getMessage();
        var_dump($e->getMessage());
    }

## Unit Tests

This library uses PHPUnit for unit testing. In order to run the unit tests, you'll first need
to install the dependencies of the project using Composer: `php composer.phar install --dev`.
You can then run the tests using `vendor/bin/phpunit`. The library comes with a set of mocked responses
from the Chain API for running the unit tests.

## Contributions

Patches, bug fixes, feature requests, and pull requests are welcome.
