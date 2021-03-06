---
title: Microsoft Intune を使用して macOS システムとカーネル拡張機能を作成する - Azure | Microsoft Docs
titleSuffix: ''
description: システム拡張機能またはカーネル拡張機能を構成する macOS デバイス プロファイルを追加または作成します。このプロファイルでは、Microsoft Intune でのユーザーによるオーバーライド、チーム識別子の追加、バンドルとチーム識別子の追加を許可します。
keywords: ''
author: MandiOhlinger
ms.author: mandia
manager: dougeby
ms.date: 05/07/2020
ms.topic: how-to
ms.service: microsoft-intune
ms.subservice: configuration
ms.localizationpriority: medium
ms.technology: ''
ms.reviewer: annovich
ms.suite: ems
search.appverid: MET150
ms.custom: intune-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce8218c9f8fb8757f0aef892f0854f1c386a8bd
ms.sourcegitcommit: 302556d3b03f1a4eb9a5a9ce6138b8119d901575
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83990303"
---
# <a name="add-macos-system-and-kernel-extensions-in-intune"></a>Intune で macOS システムとカーネルの拡張機能を追加する

> [!NOTE]
> システム拡張機能が macOS カーネル拡張機能に取って代わります。 詳細については、[サポート ヒント:Intune では、macOS Catalina 10.15 のカーネル拡張機能の代わりにシステム拡張機能を使用します](https://techcommunity.microsoft.com/t5/intune-customer-success/support-tip-using-system-extensions-instead-of-kernel-extensions/ba-p/1191413)。

macOS デバイスでは、カーネル拡張機能とシステム拡張機能を追加できます。 カーネル拡張機能とシステム拡張機能の両方で、ユーザーは、オペレーティング システムのネイティブ機能を拡張するアプリ拡張機能をインストールできます。 カーネル拡張機能は、カーネル レベルでコードを実行します。 システム拡張機能は、厳しく制御されたユーザー領域で実行されます。

常にデバイスでの読み込みが許可される拡張機能を追加するには、Microsoft Intune を使用します。 Intune では、"構成プロファイル" を使用して、お客様の組織のニーズに合わせてこのような設定を作成およびカスタマイズします。 これらの機能をプロファイルに追加した後は、そのプロファイルをお客様の組織内の macOS デバイスにプッシュまたは展開します。

この記事では、システム拡張機能とカーネル拡張機能について説明します。 また、Intune で拡張機能を使用してデバイス構成プロファイルを作成する方法についても説明します。

## <a name="system-extensions"></a>システム拡張機能

システム拡張機能はユーザー領域で実行され、カーネルへアクセスしません。 カーネル レベルの攻撃を制限しながら、セキュリティを強化し、さらにエンド ユーザー コントロールを提供することを目的としています。 次の拡張機能を使用できます。

- ドライバーの拡張機能 (USB、ネットワーク インターフェイス カード (NIC)、シリアル コントローラー、およびヒューマン インターフェイス デバイス (HID) 用ドライバーを含む)
- ネットワーク拡張機能 (コンテンツ フィルター、DNS プロキシ、VPN クライアントを含む)
- エンドポイントのセキュリティ拡張機能 (エンドポイントの検出、エンドポイントの応答、ウイルス対策を含む)

システム拡張機能は、アプリのバンドルに含まれており、アプリからインストールされます。

システム拡張機能の詳細については、[システム拡張機能](https://developer.apple.com/documentation/systemextensions) (Apple の Web サイトが開きます) に関するページを参照してください。

## <a name="kernel-extensions"></a>カーネル拡張機能

カーネル拡張機能は、カーネル レベルで機能を追加します。 これらの機能では、通常のプログラムがアクセスできない OS の部分にアクセスします。 組織には、アプリやデバイスの機能などでは利用できない特定のニーズや要件がある場合があります。

たとえば、デバイスで悪意のあるコンテンツをスキャンするウイルス スキャン プログラムがあるとします。 このウイルス スキャン プログラムのカーネル拡張機能を、Intune で許可されるカーネル拡張機能として追加できます。 次に、拡張機能を macOS デバイスに "割り当て" ます。

この機能を使用すると、管理者は、ユーザーが Intune でカーネル拡張機能をオーバーライドしたり、チーム識別子を追加したり、特定のカーネル拡張機能を追加したりすることを許可できます。

カーネル拡張機能の詳細については、[カーネル拡張機能](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/Extend/Extend.html) (Apple の Web サイトが開きます) に関するページを参照してください。

## <a name="prerequisites"></a>[前提条件]

- この機能は、以下に適用されます。

  - macOS 10.13.2 以降 (カーネル拡張機能)
  - macOS 10.15 以降 (システム拡張機能)

  macOS 10.15 から 10.15.4 では、カーネル拡張機能とシステム拡張機能を並行して実行できます。

- この機能を使用するには、次のようなデバイスが必要です。

  - Apple の Device Enrollment Program (DEP) を使用して Intune に登録されている。 詳細については、[macOS デバイスの自動登録](../enrollment/device-enrollment-program-enroll-macos.md)に関する記事を参照してください。

    または

  - "ユーザー承認済みの登録" (Apple の用語) を使用して Intune に登録されている。 詳細については、「[macOS High Sierra のカーネル機能拡張の変更点について準備を進める](https://support.apple.com/en-us/HT208019)」 (Apple の Web サイトが開きます) を参照してください。

## <a name="what-you-need-to-know"></a>知っておく必要がある情報

- 未署名のレガシ カーネル拡張機能とシステム拡張機能を追加できます。
- 拡張機能の正しいチーム識別子とバンドル ID を入力してください。 Intune では、入力した値は検証されません。 間違った情報を入力すると、デバイス上の拡張機能は機能しません。 チーム識別子の長さは、英数字で厳密に 10 文字です。

> [!NOTE]
> Apple から、すべてのソフトウェアの署名と公証 (Notarization) に関する情報が公開されています。 macOS 10.14.5 以降では、Intune を使用して展開されたカーネル拡張機能は、Apple の公証ポリシーを満たしている必要はありません。
>
> この公証ポリシー、および更新または変更の詳細については、次のリソースを参照してください。
>
> - [配布の前にアプリを公証する](https://developer.apple.com/documentation/security/notarizing_your_app_before_distribution) (Apple の Web サイトが開きます) 
> - [macOS High Sierra のカーネル機能拡張の変更点について準備を進める](https://support.apple.com/en-us/HT208019) (Apple の Web サイトが開きます)

## <a name="create-the-profile"></a>プロファイルの作成

1. [Microsoft Endpoint Manager 管理センター](https://go.microsoft.com/fwlink/?linkid=2109431)にサインインします。
2. **[デバイス]**  >  **[構成プロファイル]**  >  **[プロファイルの作成]** の順に選択します。
3. 次のプロパティを入力します。

    - **[プラットフォーム]** : **[macOS]** を選択します
    - **[プロファイル]** : **[拡張機能]** を選択します。

4. **[作成]** を選択します。
5. **[Basics]\(基本\)** で次のプロパティを入力します。

    - **名前**:ポリシーのわかりやすい名前を入力します。 後で簡単に識別できるよう、ポリシーに名前を付けます。 たとえば、適切なポリシー名は **macOS: デバイスのカーネル拡張機能にウイルス対策スキャンを追加するなどです**。
    - **説明**:ポリシーの説明を入力します。 この設定は省略可能ですが、推奨されます。

6. **[次へ]** を選択します。

7. **[構成設定]** で、次の設定を構成します。

    - [macOS](kernel-extensions-settings-macos.md)

8. **[次へ]** を選択します。
9. **スコープ タグ** (オプション) で、`US-NC IT Team` や `JohnGlenn_ITDepartment` など、特定の IT グループにプロファイルをフィルター処理するためのタグを割り当てます。 スコープ タグの詳細については、[分散 IT に RBAC とスコープのタグを使用する](../fundamentals/scope-tags.md)に関するページを参照してください。

    **[次へ]** を選択します。

10. **[割り当て]** で、プロファイルを受け取るユーザーまたはグループを選択します。 プロファイルの割り当ての詳細については、[ユーザーおよびデバイス プロファイルの割り当て](device-profile-assign.md)に関するページを参照してください。

    **[次へ]** を選択します。

11. **[確認と作成]** で、設定を確認します。 **[作成]** を選択すると、変更内容が保存され、プロファイルが割り当てられます。 また、ポリシーがプロファイル リストに表示されます。

## <a name="next-steps"></a>次のステップ

プロファイルが作成されると、割り当てることができます。 次に、[プロファイルを割り当て](device-profile-assign.md)、[その状態を監視](device-profile-monitor.md)します。
