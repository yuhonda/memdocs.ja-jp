---
title: Microsoft Intune で macOS デバイスにカスタム設定を追加する - Azure | Microsoft Docs
titleSuffix: ''
description: Apple Configurator または Apple Profile Manager ツールから macOS の設定をエクスポートした後、Microsoft Intune にそれらの設定をインポートします。 これらの設定では、macOS デバイスでのカスタム設定と機能を作成、使用、制御できます。 このカスタム プロファイルを組織内の macOS デバイスに割り当てたり配布したりして、ベースラインまたは基準を作成できます。
keywords: ''
author: MandiOhlinger
ms.author: mandia
manager: dougeby
ms.date: 05/04/2020
ms.topic: reference
ms.service: microsoft-intune
ms.subservice: configuration
ms.localizationpriority: medium
ms.technology: ''
ms.reviewer: kakyker
ms.suite: ems
search.appverid: MET150
ms.custom: intune-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: e92315377a839a537dfc4c2da00d282d2cddf58f
ms.sourcegitcommit: 48005a260bcb2b97d7fe75809c4bf1552318f50a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2020
ms.locfileid: "83429086"
---
# <a name="use-custom-settings-for-macos-devices-in-microsoft-intune"></a>Microsoft Intune で macOS デバイス用のカスタム設定を使用する

Microsoft Intune では、"カスタム プロファイル" を使用して macOS デバイス用のカスタム設定を追加または作成できます。 カスタム プロファイルは Intune の機能です。 Intune に組み込まれていないデバイスの設定と機能を追加するように設計されています。

macOS デバイスを使用するとき、Intune にカスタム設定を取り込むには 2 つの方法があります。

- [Apple Configurator](https://itunes.apple.com/app/apple-configurator-2/id1037126344?mt=12)
- [Apple Profile Manager](https://support.apple.com/profile-manager)

これらのツールを使用して、設定を構成プロファイルにエクスポートすることができます。 Intune では、このファイルをインポートし、macOS のユーザーとデバイスにプロファイルを割り当てます。 割り当てが完了すると、設定が配布されます。 また、組織内の macOS のベースラインまたは標準も作成されます。

この記事では、Apple Configurator と Apple Profile Manager の使用に関するいくつかのガイダンスを提供し、構成できるプロパティについて説明します。

## <a name="before-you-begin"></a>始める前に

[macOS カスタム プロファイルを作成します](custom-settings-configure.md)。

## <a name="what-you-need-to-know"></a>知っておく必要がある情報

- **Apple Configurator** を使用して構成プロファイルを作成するときは、エクスポートする設定が、デバイスの macOS バージョンと互換性があることを確認してください。 互換性のない設定の解決については、[Apple 開発者](https://developer.apple.com/) Web サイトで「**Configuration Profile Reference**」(構成プロファイル リファレンス) および「**Mobile Device Management Protocol Reference**」(モバイル デバイス管理プロトコル リファレンス) を検索してください。

- **Apple Profile Manager** を使用するときは、次のことを確認してください。

  - Profile Manager で[モバイル デバイス管理](https://help.apple.com/serverapp/mac/5.7/#/apd05B9B761-D390-4A75-9251-E9AD29A61D0C)を有効にします。
  - Profile Manager で [macOS デバイス](https://help.apple.com/profilemanager/mac/5.7/#/pm9onzap1984)を追加します。
  - Profile Manager でデバイスを追加した後、 **[Under the Library]\(ライブラリの下\)**  >  **[Devices]\(デバイス\)** に移動し、デバイスを選択し、 **[Settings]\(設定\)** を選択します。 デバイスに対する全般、セキュリティ、プライバシー、ディレクトリ、および証明書の設定を入力します。

    このファイルをダウンロードして保存します。 このファイルを Intune プロファイルに入力します。 

  - Apple Profile Manager からエクスポートする設定が、デバイスの macOS バージョンと互換性があることを確認してください。 互換性のない設定の解決については、[Apple 開発者](https://developer.apple.com/) Web サイトで「**Configuration Profile Reference**」(構成プロファイル リファレンス) および「**Mobile Device Management Protocol Reference**」(モバイル デバイス管理プロトコル リファレンス) を検索してください。

## <a name="custom-configuration-profile-settings"></a>カスタム構成プロファイルの設定

- **構成プロファイル名**: ポリシーの名前を入力します。 この名前は、デバイス上と、Intune の状態に表示されます。
- **[構成プロファイル ファイル]** :Apple Configurator または Apple Profile Manager を使用して作成した `.xml` または `.mobileconfig` プロファイルを参照します。 最大ファイル サイズは 1,000,000 バイト (1 MB 未満) です。 インポートしたファイルが表示されます。 ファイルは追加した後に**削除**することもできます。

  また、`.mobileconfig` ファイルにデバイス トークンを追加することもできます。 デバイス トークンは、デバイス固有の情報を追加するために使用されます。 たとえば、シリアル番号を表示するには、`{{serialnumber}}` と入力します。 デバイスでは、各デバイスに固有の `123456789ABC` のようなテキストが表示されます。 変数を入力するときは、必ず中かっこ `{{ }}` を使用してください。 [アプリの構成トークン](../apps/app-configuration-policies-use-ios.md#tokens-used-in-the-property-list)に関する記事には、使用できる変数の一覧が掲載されています。 `deviceid` または他のデバイス固有の値を使用することもできます。

  > [!NOTE]
  > 変数は UI で検証されず、大文字と小文字が区別されます。 その結果、不適切な入力で保存されたプロファイルが表示される場合があります。 たとえば、`{{deviceid}}` の代わりに `{{DeviceID}}` を入力した場合、リテラル文字列がデバイスの一意の ID の代わりに表示されます。 必ず、正しい情報を入力してください。

## <a name="next-steps"></a>次のステップ

[プロファイルを割り当て](device-profile-assign.md)、[その状態を監視](device-profile-monitor.md)します。

[iOS または iPadOS デバイス上でカスタム プロファイル](custom-settings-ios.md)を作成します。
