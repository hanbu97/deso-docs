---
description: Description of flags related to the Onboarding proces and starter DESO
---

# 入门

注意：需要提供 Starter DESO Seed才能向用户发送DESO，以验证他们的手机号码或通过Jumio进行验证。

## Starter DESO Seed

`--starter-deso-seed`

Type: String

Default: None

用于向通过手机验证或通过Jumio验证的用户发送DESO的种子短语。

Seed phrase that is used to send DESO to users who go through [phone-number-verification.md](phone-number-verification.md "mention") or , and to [#comp-profile-creation](onboarding.md#comp-profile-creation "mention")

## Starter DESO Nanos

`--starter-deso-nanos`

Type: Integer

Default: 1000000

The amount of DESO given for verifying a phone number. Only active if [#starter-deso-seed](onboarding.md#starter-deso-seed "mention") is set and funded. 1000000 nanos = 0.001 DESO

## Starter Prefix Nanos Map

`--starter-prefix-nanos-map`

Type: String

Default: None

A comma-separated list of 'prefix=nanos' mappings, where prefix is a phone number prefix such as "+1". These mappings allow the node operator to specify custom amounts of DESO to users verifying their phone numbers based on the country they're in. This is useful as it is more expensive for attackers to get phone numbers from certain countries. An example string would be '+1=2000000,+2=2000000' which would pay user's with US phone number 0.002 DESO (2000000 nanos)

## Comp Profile Creation

`--comp-profile-creation`

Type: Boolean

Default: False

If true, the public key derived from the [#starter-deso-seed](onboarding.md#starter-deso-seed "mention") will send DESO to a public key when the public key makes a request to [#update-profile](../construct-transactions/social-transactions-api.md#update-profile "mention") to construct an UpdateProfile transaction that creates a profile if the public key has verified their phone number or verified themselves thru the Jumio process.

## Min Satoshis For Profile

`--min-satoshis-for-profile`

Deprecated
