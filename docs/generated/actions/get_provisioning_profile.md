<!--
This file is auto-generated and will be re-generated every time the docs are updated.
To modify it, go to its source at https://github.com/fastlane/fastlane/blob/master/fastlane/lib/fastlane/actions/get_provisioning_profile.rb
-->

# get_provisioning_profile


Generates a provisioning profile, saving it in the current folder (via _sigh_)




<p align="center">
  <img src="/img/actions/sigh.png" width="250">
</p>

###### Because you would rather spend your time building stuff than fighting provisioning

_sigh_ can create, renew, download and repair provisioning profiles (with one command). It supports App Store, Ad Hoc, Development and Enterprise profiles and supports nice features, like auto-adding all test devices.

-------

<p align="center">
    <a href="#features">Features</a> &bull;
    <a href="#usage">Usage</a> &bull;
    <a href="#resign">Resign</a> &bull;
    <a href="#how-does-it-work">How does it work?</a>
</p>

-------

# Features

- **Download** the latest provisioning profile for your app
- **Renew** a provisioning profile, when it has expired
- **Repair** a provisioning profile, when it is broken
- **Create** a new provisioning profile, if it doesn't exist already
- Supports **App Store**, **Ad Hoc** and **Development** profiles
- Support for **multiple Apple accounts**, storing your credentials securely in the Keychain
- Support for **multiple Teams**
- Support for **Enterprise Profiles**

To automate iOS Push profiles you can use [_pem_](https://docs.fastlane.tools/actions/pem/).


### Why not let Xcode do the work?

- _sigh_ can easily be integrated into your CI-server (e.g. Jenkins)
- Xcode sometimes invalidates [all existing profiles](/img/actions/SignErrors.png)
- You have control over what happens
- You still get to have the signing files, which you can then use for your build scripts or store in git

See _sigh_ in action:

![img/actions/sighRecording.gif](/img/actions/sighRecording.gif)

# Usage

**Note**: It is recommended to use [_match_](https://docs.fastlane.tools/actions/match/) according to the [codesigning.guide](https://codesigning.guide) for generating and maintaining your provisioning profiles. Use _sigh_ directly only if you want full control over what's going on and know more about codesigning.

```no-highlight
fastlane sigh
```

Yes, that's the whole command!

_sigh_ will create, repair and download profiles for the App Store by default.

You can pass your bundle identifier and username like this:

```no-highlight
fastlane sigh -a com.krausefx.app -u username
```

If you want to generate an **Ad Hoc** profile instead of an App Store profile:

```no-highlight
fastlane sigh --adhoc
```

If you want to generate a **Development** profile:

```no-highlight
fastlane sigh --development
```

To generate the profile in a specific directory:

```no-highlight
fastlane sigh -o "~/Certificates/"
```

To download all your provisioning profiles use

```no-highlight
fastlane sigh download_all
```

Optionally, use `fastlane sigh download_all --download_xcode_profiles` to also include the Xcode managed provisioning profiles

For a list of available parameters and commands run

```no-highlight
fastlane action sigh
```

### Advanced

By default, _sigh_ will install the downloaded profile on your machine. If you just want to generate the profile and skip the installation, use the following flag:

```no-highlight
fastlane sigh --skip_install
```

To save the provisioning profile under a specific name, use the -q option:

```no-highlight
fastlane sigh -a com.krausefx.app -u username -q "myProfile.mobileprovision"
```

If for some reason you don't want _sigh_ to verify that the code signing identity is installed on your local machine:

```no-highlight
fastlane sigh --skip_certificate_verification
```

If you need the provisioning profile to be renewed regardless of its state use the `--force` option. This gives you a profile with the maximum lifetime. `--force` will also add all available devices to this profile.

```no-highlight
fastlane sigh --force
```

By default, _sigh_ will include all certificates on development profiles, and first certificate on other types. If you need to specify which certificate to use you can either use the environment variable `SIGH_CERTIFICATE`, or pass the name or expiry date of the certificate as argument:

```no-highlight
fastlane sigh -c "SunApps GmbH"
```

For a list of available parameters and commands run

```no-highlight
fastlane action sigh
```


### Use with [_fastlane_](https://fastlane.tools)

_sigh_ becomes really interesting when used in [_fastlane_](https://fastlane.tools) in combination with [_cert_](https://docs.fastlane.tools/actions/cert/).

Update your `Fastfile` to contain the following code:

```ruby
lane :beta do
  cert
  sigh(force: true)
end
```

`force: true` will make sure to re-generate the provisioning profile on each run.
This will result in _sigh_ always using the correct signing certificate, which is installed on the local machine.


# Repair

_sigh_ can automatically repair all your existing provisioning profiles which are expired or just invalid.

```no-highlight
fastlane sigh repair
```

# Resign

If you generated your `ipa` file but want to apply a different code signing onto the ipa file, you can use `sigh resign`:

```no-highlight
fastlane sigh resign
```

_sigh_ will find the ipa file and the provisioning profile for you if they are located in the current folder.

You can pass more information using the command line:

```no-highlight
fastlane sigh resign ./path/app.ipa --signing_identity "iPhone Distribution: Felix Krause" -p "my.mobileprovision"
```

# Manage

With `sigh manage` you can list all provisioning profiles installed locally.

```no-highlight
fastlane sigh manage
```

Delete all expired provisioning profiles

```no-highlight
fastlane sigh manage -e
```

Or delete all `iOS Team Provisioning Profile` by using a regular expression

```no-highlight
fastlane sigh manage -p "iOS\ ?Team Provisioning Profile:"
```

## Environment Variables

Run `fastlane action sigh` to get a list of all available environment variables.

If you're using [_cert_](https://docs.fastlane.tools/actions/cert/) in combination with [_fastlane_](https://fastlane.tools) the signing certificate will automatically be selected for you. (make sure to run _cert_ before _sigh_)

# How does it work?

_sigh_ will access the `iOS Dev Center` to download, renew or generate the `.mobileprovision` file. It uses [_spaceship_](https://spaceship.airforce) to communicate with Apple's web services.

## How is my password stored?
_sigh_ uses the [CredentialsManager](https://github.com/fastlane/fastlane/tree/master/credentials_manager) from _fastlane_.

# Tips

## Use 'ProvisionQL' for advanced Quick Look in Finder

Install [ProvisionQL](https://github.com/ealeksandrov/ProvisionQL).

It will show you `mobileprovision` files like this:
![img/actions/QuickLookScreenshot-Provision.png](/img/actions/QuickLookScreenshot-Provision.png)

## App Identifier couldn't be found

If you also want to create a new App Identifier on the Apple Developer Portal, check out [_produce_](https://docs.fastlane.tools/actions/produce/), which does exactly that.

## What happens to my Xcode managed profiles?

_sigh_ will never touch or use the profiles which are created and managed by Xcode. Instead _sigh_ will manage its own set of provisioning profiles.

<hr />


get_provisioning_profile ||
---|---
Supported platforms | ios, mac
Author | @KrauseFx
Returns | The UUID of the profile sigh just fetched/generated



## 3 Examples

```ruby
get_provisioning_profile
```

```ruby
sigh # alias for "get_provisioning_profile"
```

```ruby
get_provisioning_profile(
  adhoc: true,
  force: true,
  filename: "myFile.mobileprovision"
)
```





## Parameters

Key | Description | Default
----|-------------|--------
  `adhoc` | Setting this flag will generate AdHoc profiles instead of App Store Profiles | `false`
  `developer_id` | Setting this flag will generate Developer ID profiles instead of App Store Profiles | `false`
  `development` | Renew the development certificate instead of the production one | `false`
  `skip_install` | By default, the certificate will be added to your local machine. Setting this flag will skip this action | `false`
  `force` | Renew provisioning profiles regardless of its state - to automatically add all devices for ad hoc profiles | `false`
  `app_identifier` | The bundle identifier of your app | [*](#parameters-legend-dynamic)
  `api_key_path` | Path to your App Store Connect API Key JSON file (https://docs.fastlane.tools/app-store-connect-api/#using-fastlane-api-key-json-file) | 
  `api_key` | Your App Store Connect API Key information (https://docs.fastlane.tools/app-store-connect-api/#using-fastlane-api-key-hash-option) | 
  `username` | Your Apple ID Username | [*](#parameters-legend-dynamic)
  `team_id` | The ID of your Developer Portal team if you're in multiple teams | [*](#parameters-legend-dynamic)
  `team_name` | The name of your Developer Portal team if you're in multiple teams | [*](#parameters-legend-dynamic)
  `provisioning_name` | The name of the profile that is used on the Apple Developer Portal | 
  `ignore_profiles_with_different_name` | Use in combination with :provisioning_name - when true only profiles matching this exact name will be downloaded | `false`
  `output_path` | Directory in which the profile should be stored | `.`
  `cert_id` | The ID of the code signing certificate to use (e.g. 78ADL6LVAA)  | 
  `cert_owner_name` | The certificate name to use for new profiles, or to renew with. (e.g. "Felix Krause") | 
  `filename` | Filename to use for the generated provisioning profile (must include .mobileprovision) | 
  `skip_fetch_profiles` | Skips the verification of existing profiles which is useful if you have thousands of profiles | `false`
  `include_all_certificates` | Include all matching certificates in the provisioning profile. Works only for the 'development' provisioning profile type | `false`
  `skip_certificate_verification` | Skips the verification of the certificates for every existing profiles. This will make sure the provisioning profile can be used on the local machine | [*](#parameters-legend-dynamic)
  `platform` | Set the provisioning profile's platform (i.e. ios, tvos, macos, catalyst) | `ios`
  `readonly` | Only fetch existing profile, don't generate new ones | `false`
  `template_name` | The name of provisioning profile template. If the developer account has provisioning profile templates (aka: custom entitlements), the template name can be found by inspecting the Entitlements drop-down while creating/editing a provisioning profile (e.g. "Apple Pay Pass Suppression Development") | 
  `fail_on_name_taken` | Should the command fail if it was about to create a duplicate of an existing provisioning profile. It can happen due to issues on Apple Developer Portal, when profile to be recreated was not properly deleted first | `false`

<em id="parameters-legend-dynamic">* = default value is dependent on the user's system</em>


<hr />



## Lane Variables

Actions can communicate with each other using a shared hash `lane_context`, that can be accessed in other actions, plugins or your lanes: `lane_context[SharedValues:XYZ]`. The `get_provisioning_profile` action generates the following Lane Variables:

SharedValue | Description 
------------|-------------
  `SharedValues::SIGH_PROFILE_PATH` | A path in which certificates, key and profile are exported
  `SharedValues::SIGH_PROFILE_PATHS` | Paths in which certificates, key and profile are exported
  `SharedValues::SIGH_UUID` | UUID (Universally Unique IDentifier) of a provisioning profile
  `SharedValues::SIGH_NAME` | The name of the profile
  `SharedValues::SIGH_PROFILE_TYPE` | The profile type, can be app-store, ad-hoc, development, enterprise, developer-id, can be used in `build_app` as a default value for `export_method`

To get more information check the [Lanes documentation](https://docs.fastlane.tools/advanced/lanes/#lane-context).
<hr />


## Documentation

To show the documentation in your terminal, run
```no-highlight
fastlane action get_provisioning_profile
```

<hr />

## CLI

It is recommended to add the above action into your `Fastfile`, however sometimes you might want to run one-offs. To do so, you can run the following command from your terminal

```no-highlight
fastlane run get_provisioning_profile
```

To pass parameters, make use of the `:` symbol, for example

```no-highlight
fastlane run get_provisioning_profile parameter1:"value1" parameter2:"value2"
```

It's important to note that the CLI supports primitive types like integers, floats, booleans, and strings. Arrays can be passed as a comma delimited string (e.g. `param:"1,2,3"`). Hashes are not currently supported.

It is recommended to add all _fastlane_ actions you use to your `Fastfile`.

<hr />

## Source code

This action, just like the rest of _fastlane_, is fully open source, <a href="https://github.com/fastlane/fastlane/blob/master/fastlane/lib/fastlane/actions/get_provisioning_profile.rb" target="_blank">view the source code on GitHub</a>

<hr />

<a href="/actions/"><b>Back to actions</b></a>
