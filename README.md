# Flutter App Deploy Workflow

## Target Project

This workflow is designed for mobile applications developed with Flutter (Android/iOS).
It provides GitHub Actions configurations to automate build, code signing, and deployment processes in a CI/CD pipeline.

## Prerequisites

- Flutter SDK must be installed and configured in your repository.
- For Android builds: Java (default 17), keystore, and a `.properties`-file-based signing config is required.
- For iOS builds: Xcode (default 26.0), provisioning profiles, and code signing certificates are required.
- Fastlane must be set up in both `android/fastlane` and `ios/fastlane` directories with appropriate `Appfile` and `Fastfile`.
- Fastlane must be installed via bundler in project root.

## Directory Structure Example

```
project-root/
├── android/
│   ├── fastlane/
│   │   └── Appfile
│   │   └── Fastfile
├── ios/
│   ├── fastlane/
│   │   └── Appfile
│   │   └── Fastfile
└── ...
```

## Input

### Android Workflow (.github/workflows/android.yml)

| Name                | Description                                    | Required | Default                |
|---------------------|------------------------------------------------|----------|------------------------|
| build-number        | Build number                                   | Yes      | -                      |
| build-name          | Build name                                     | Yes      | -                      |
| keystore-file       | Path to the keystore file                      |          | android/main.keystore  |
| key-properties-file | Path to the signing config .properties file    |          | android/key.properties |
| java-version        | Java version                                   |          | 17                     |
| build-options       | Additional options for `flutter build` command |          | ''                     |
| wif-provider        | Workload Identity Federation Provider          | Yes      | -                      |
| wif-service-account | Workload Identity Federation Service Account   | Yes      | -                      |
| wif-audience        | Workload Identity Federation Audience          |          | -                      |

### iOS Workflow (.github/workflows/ios.yml)

| Name                      | Description                                    | Required | Default                    |
|---------------------------|------------------------------------------------|----------|----------------------------|
| build-number              | Build number                                   | Yes      | -                          |
| build-name                | Build name                                     | Yes      | -                          |
| build-options             | Additional options for `flutter build` command |          | -                          |
| xcode-version             | Xcode version                                  |          | 26.0                       |
| export-options-plist-file | Path to ExportOptions.plist file               |          | ios/ExportOptions.plist    |
| asc-key-file-path         | Path to App Store Connect API Key .p8 file     |          | ios/secrets/ASC_AuthKey.p8 |

## Secret

### Android

| Name                        | Description                                    | Required |
|-----------------------------|------------------------------------------------|----------|
| keystore_base64             | Base64 encoded keystore file                   | Yes      |
| key_properties_base64       | Base64 encoded signing config .properties file | Yes      |
| google_services_json_base64 | Base64 encoded google-services.json            |          |

### iOS

| Name                             | Description                                       | Required |
|----------------------------------|---------------------------------------------------|----------|
| provisioning_profile_1_base64    | Base64 encoded provisioning profile 1             | Yes      |
| provisioning_profile_2_base64    | Base64 encoded additional provisioning profile    |          |
| codesign_cert_base64             | Base64 encoded .p12 file for code signing         | Yes      |
| codesign_cert_pw                 | Password for the .p12 file                        | Yes      |
| google_service_info_plist_base64 | Base64 encoded GoogleService-Info.plist           |          |
| asc_key_id                       | App Store Connect API Key ID                      | Yes      |
| asc_issuer_id                    | App Store Connect API Key Issuer ID               | Yes      |
| asc_key_base64                   | Base64 encoded App Store Connect API Key .p8 file |          |

## Usage Example

Call this workflow from another workflow in your repository:

```
name: Deploy Android App
on:
  workflow_dispatch:
jobs:
  deploy:
    uses: chika3742/flutter-app-deploy/.github/workflows/android.yml@main
    with:
      build-number: '1'
      build-name: '1.0.0'
      wif-provider: ${{ secrets.WIF_PROVIDER }}
      wif-service-account: ${{ secrets.WIF_SERVICE_ACCOUNT }}
    secrets:
      keystore_base64: ${{ secrets.KEYSTORE_BASE64 }}
      key_properties_base64: ${{ secrets.KEY_PROPERTIES_BASE64 }}
```

## License

This project is licensed under the terms of the LICENSE file in this repository.
