---
title: Intune でのデータのセキュリティと共有
titleSuffix: Microsoft Intune
description: Intune で個人データがどのように保護され、共有されるかについて学習します。
keywords: プライバシー, データ
author: ErikjeMS
ms.author: erikje
manager: dougeby
ms.date: 09/01/2020
ms.topic: conceptual
ms.service: microsoft-intune
ms.subservice: protect
ms.localizationpriority: high
ms.technology: ''
ms.assetid: 68921fd6-5f50-456c-a3af-83d7bc4b134b
ms.reviewer: angerobe
ms.suite: ems
search.appverid: MET150
ms.custom: intune-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34e99a53165afdccd5ee9a2f3f190c78f673a507
ms.sourcegitcommit: 15450a1e92d9f67f74ae619ffe192c15948107c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89516258"
---
# <a name="data-security-and-sharing-in-intune"></a>Intune でのデータのセキュリティと共有


## <a name="data-security"></a>データ セキュリティ

Microsoft Intune は、Microsoft Enterprise Mobility およびセキュリティ スイート クラウドのサービス内容の重要なコンポーネントです。 [データ ガバナンス戦略](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx)をサポートするため、すべての Microsoft クラウド サービスは、[Microsoft のプライバシー](https://www.microsoft.com/en-us/trustcenter/privacy)と [Microsoft セキュリティ](https://www.microsoft.com/en-us/trustcenter/security/)の手法で開発されています。  

Microsoft Intune は、Microsoft Azure サービス チームがデータ侵害プロセスから保護するために実施しているのと同じ技術的および組織的な方法に従っています。

詳細については、「[Service Trust Portal](https://www.microsoft.com/en-us/TrustCenter/stp)」を参照してください。

### <a name="data-breach-reporting"></a>データ侵害レポート

顧客レポート可能なセキュリティ インシデント (CRSI) が識別されると、顧客に通知されます。 このプロセスには、Microsoft 365 チームと連携して、Intune を使用している Microsoft 365 のすべての顧客に侵害通知を送信することが含まれます。

## <a name="data-sharing"></a>データ共有

テナント管理者が特定の機能 (Apple Device Enrollment Program など) を有効にすると、Microsoft Intune は、適切なサード パーティとデータを共有するため、管理者の同意を得ます。 このような場合、Intune は以下と個人データを共有する場合があります。

- Microsoft のエージェントとして活動しているサード パーティ。
- Microsoft のエージェントとしては活動していないが、テナント管理者が明示的に Intune にそのように活動するための権限を与えた場合にのみエージェントとして活動するサード パーティ。

Microsoft のエージェントとして活動しているすべてのサードパーティは、[オンライン サービスの下請業者の一覧](https://aka.ms/Online_Serv_Subcontractor_List)に関するページに含まれています。

このようなエンティティとのデータの共有は、顧客とテクニカル サポート、サービスのメンテナンス、およびその他の作業を支援するために行われます。

テナントのサード パーティとのコントラクトは、サード パーティのサービスで保持されている Intune の個人データに影響を与えます。 また、Intune にサード パーティ サービスにデータを送信する権限も付与します。  

特定のサード パーティと共有されるデータについては、次の記事を参照してください。
- [Intune から Apple に送られるデータ](data-intune-sends-to-apple.md)
- [Intune から Google に送られるデータ](data-intune-sends-to-google.md)
- [Apple から Intune に送られるデータ](data-apple-sends-to-intune.md)
- [Google から Intune に送られるデータ](data-google-sends-to-intune.md)
- [Jamf Pro から Intune に送られるデータ](data-jamf-sends-to-intune.md)

### <a name="microsoft-endpoint-configuration-manager-data-sharing"></a>Microsoft Endpoint Configuration Manager のデータ共有

Microsoft Intune では、データが Configuration Manager と共有されることはありません。 Configuration Manager は、お客様が直接デプロイ、管理、操作するオンプレミス製品です。 Configuration Manager で収集される診断データおよび使用状況データは、今後のリリースでインストールのエクスペリエンス、品質、セキュリティを向上させるためだけに使用されます。

詳細については、[Configuration Manager の診断結果と使用状況データ](/configmgr/core/plan-design/diagnostics/diagnostics-and-usage-data)に関するページをご覧ください。 


## <a name="next-steps"></a>次のステップ

Intune で個人データを[表示および修正](privacy-data-view-correct.md)する方法を確認します。
