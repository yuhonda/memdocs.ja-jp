---
title: コンテンツ管理の基礎
titleSuffix: Configuration Manager
description: Configuration Manager のツールとオプションを使用して、展開するコンテンツを管理します。
ms.date: 07/13/2020
ms.prod: configuration-manager
ms.technology: configmgr-core
ms.topic: conceptual
ms.assetid: c201be2a-692c-4d67-ac95-0a3afa5320fe
author: aczechowski
ms.author: aaroncz
manager: dougeby
ms.openlocfilehash: 11649452012de33ef1e62007d71466d5a45c56ca
ms.sourcegitcommit: 99084d70c032c4db109328a4ca100cd3f5759433
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88698607"
---
# <a name="fundamental-concepts-for-content-management-in-configuration-manager"></a>Configuration Manager でのコンテンツ管理の基本的な概念

*適用対象:Configuration Manager (Current Branch)*

Configuration Manager では、ソフトウェア コンテンツを管理するためのツールとオプションの信頼性の高いシステムがサポートされます。 アプリケーション、パッケージ、ソフトウェア更新プログラム、OS の展開などのソフトウェアの展開にはすべてコンテンツが必要です。 Configuration Manager は、サイト サーバーと配布ポイントの両方にコンテンツを格納します。 このコンテンツを地点間で転送するときに、大きなネットワーク帯域幅が必要になります。 コンテンツ管理インフラストラクチャを効果的に計画および使用するには、まず、利用可能なオプションと構成を理解します。 その後、ネットワーク環境やコンテンツ展開ニーズに最適な使い方を検討します。  

> [!TIP]  
> コンテンツ配布プロセスの詳細、およびコンテンツ配布に関する一般的な問題の診断と解決に役立つ情報の探し方については、「[Understanding and Troubleshooting Content Distribution in Microsoft Configuration Manager](https://support.microsoft.com/help/4000401/content-distribution-in-mcm)」(Microsoft Configuration Manager でのコンテンツ配布の理解とトラブルシューティング) をご覧ください。

コンテンツ管理の主要概念を以下のセクションに示します。 概念について追加情報または複雑な情報が必要な場合は、該当する詳細情報へのリンクが示されています。


## <a name="accounts-used-for-content-management"></a>コンテンツ管理に使用されるアカウント

コンテンツ管理では、次のアカウントを使用できます。  

### <a name="network-access-account"></a>ネットワーク アクセス アカウント

クライアントが配布ポイントに接続してコンテンツにアクセスするために使用します。 既定では、コンピューター アカウントが最初に試行されます。  

このアカウントは、リモート フォレスト内のソース配布ポイントからコンテンツをダウンロードするためにプル配布ポイントによっても使われます  

バージョン 1806 以降では、一部のシナリオでネットワーク アクセス アカウントが不要になりました。 サイトで Azure Active Directory 認証を使用した拡張 HTTP の使用を有効にすることができます。<!--1358228-->

詳細については、「[ネットワーク アクセス アカウント](accounts.md#network-access-account)」を参照してください。

### <a name="package-access-account"></a>［パッケージ アクセス アカウント］

既定では、Configuration Manager は、汎用アクセス アカウントである Users および Administrators に対して配布ポイント上のコンテンツへのアクセスを許可します。 ただし、追加のアクセス許可を構成してアクセスを制限することができます。

詳細については、「[パッケージ アクセス アカウント](accounts.md#package-access-account)」をご覧ください。


## <a name="bandwidth-throttling-and-scheduling"></a>帯域幅調整とスケジュール

調整とスケジュールのどちらも、コンテンツをサイト サーバーから配布ポイントに配布する処理を管理するためのオプションです。 これらの機能は、サイト間のファイルベースのレプリケーションに関する帯域幅の制御に似ていますが、直接の関係はありません。  

詳細については、「[ネットワーク帯域幅の管理](manage-network-bandwidth.md)」を参照してください。


## <a name="binary-differential-replication"></a>バイナリ差分レプリケーション

Configuration Manager は、バイナリ差分レプリケーション (BDR) を使用して、以前に他のサイトまたはリモート配布ポイントに配布したコンテンツを更新します。 配布ポイントに **Remote Differential Compression** 機能をインストールすると、BDR の使用時に帯域幅の使用を減らすことができます。 詳細については、「[配布ポイントの前提条件](../configs/site-and-site-system-prerequisites.md#bkmk_2012dppreq)」を参照してください。

BDR は、配布されたコンテンツの更新プログラムの送信に使われるネットワーク帯域幅を最小限に抑えます。 ファイルを変更するたびにコンテンツ ソース ファイルのセット全体が送信される代わりに、新しいまたは変更されたコンテンツのみが再送信します。  

BDR が使われていると、Configuration Manager は、以前に配布された各コンテンツ セットについて、ソース ファイルに対して行われた変更を識別します。  

- ソース コンテンツ内のファイルが変更されると、サイトではコンテンツの新しい増分バージョンが作成されます。 その後、変更されたファイルのみがレプリケーション先サイトと配布ポイントにレプリケートします。 ファイルは、名前が変更された場合、移動された場合、またはファイルの内容が変更された場合に、変更されたものと見なされます。 たとえば、以前に複数のサイトに配布したドライバー パッケージの 1 つのドライバー ファイルを置き換えた場合は、変更されたドライバー ファイルのみがレプリケートされます。  

- Configuration Manager は、全体のコンテンツ セットを再送信するまでに、最大 5 つの増分バージョンのコンテンツ セットを保持できます。 5 番目の更新の後で、コンテンツ セットを次に変更すると、サイトはコンテンツ セットの新しいバージョンを作成します。 Configuration Manager はその後、コンテンツ セットの新しいバージョンを配布し、前のセットとその増分バージョンを置き換えます。 新しいコンテンツ セットが配布されると、BDR によって、またソース ファイルのその後の増分の変更がレプリケートされます。  

BDR は、同じ階層内の親サイトと子サイト間でサポートされます。 また、BDR は、サイト内のサイト サーバーとその定期的な配布ポイント間でサポートされます。 しかし、プル配布ポイントとクラウドの配布ポイントでは、BDR によるコンテンツの転送はサポートされません。 プル配布ポイントは、ファイル レベルのデルタをサポートしており、新しいファイルは転送されますが、ファイル内のブロックは転送されません。

アプリケーションは、常にバイナリ差分レプリケーションを使用します。 パッケージの BDR はオプションであり、既定では有効になりません。 パッケージに BDR を使うには、パッケージごとにこの機能を有効にします。 パッケージを作成または編集するときに、 **[バイナリ差分レプリケーションを有効にする]** オプションを有効にします。


### <a name="bdr-or-delta-replication"></a>BDR または差分レプリケーション

<!-- SCCMDocs#1209 -->
次の一覧は、*バイナリ差分レプリケーション* (BDR) と*差分レプリケーション*の違いをまとめたものです。

#### <a name="summary-of-binary-differential-replication"></a>バイナリ差分レプリケーションの概要

- Windows の **Remote Differential Compression** の Configuration Manager の用語
- "*ブロック*" レベルの相違点
- アプリに対して常に有効
- レガシ パッケージでは省略可能
- ファイルが配布ポイントに既に存在し、変更がある場合、サイトは BDR を使用して、ファイル全体ではなく、ブロックレベルの変更をレプリケートします。 この動作は、オブジェクトで BDR を使用できるようにした場合にのみ適用されます。<!-- SCCMDocs#2026 -->

#### <a name="summary-of-delta-replication"></a>差分レプリケーションの概要

- "*ファイル*" レベルの相違点
- 既定では、構成できない
- パッケージが変更されると、サイトはパッケージ全体ではなく個々のファイルに対する変更を確認します。
    - ファイルが変更された場合は、BDR を使用して作業を行う
    - 新しいファイルがある場合は、新しいファイルをコピーする


## <a name="peer-caching-technologies"></a>ピア キャッシュ テクノロジ

<!-- SCCMDocs#1044 -->

Configuration Manager では、同じネットワーク上のピア デバイス間でコンテンツを管理するためのいくつかのオプションがサポートされています。

- [BranchCache](#branchcache)
- [配信の最適化](#delivery-optimization)
- [Configuration Manager のピア キャッシュ](#peer-cache)

次の表を使用して、これらのテクノロジの主な機能を比較します。

| 機能  | ピア&nbsp;キャッシュ  | 配信&nbsp;の最適化  | BranchCache  |
|---------|---------|---------|---------|
| サブネット間 | はい | はい | いいえ |
| 帯域幅のスロットル | はい (BITS) | はい (ネイティブ) | はい (BITS) |
| 部分コンテンツ | はい | はい | はい |
| ディスクのコントロール キャッシュ サイズ | はい | はい | はい |
| ピア ソースの検出 | 手動 (クライアント設定) | 自動 | 自動 |
| ピア検出 | 境界グループを使用した管理ポイントを経由 | DO クラウド サービス | ブロードキャスト |
| レポート | クライアント データ ソース ダッシュボード | クライアント データ ソース ダッシュボード | クライアント データ ソース ダッシュボード |
| WAN の使用の制御 | 境界グループ | DO GroupID | サブネットのみ |
| サポートされているコンテンツ | すべての ConfigMgr コンテンツ | Windows の更新プログラム、ドライバー、ストア アプリ | すべての ConfigMgr コンテンツ |
| ポリシー コントロール | クライアント エージェント設定 | クライアント エージェント設定 (一部) | クライアント エージェント設定 |

### <a name="recommendations"></a>推奨事項

- 最新の管理:Intune などの最新のツールを既に使用している場合は、配信の最適化を実装します

- Configuration Manager と共同管理:ピア キャッシュと配信の最適化を組み合わせて使用します。 オンプレミスの配布ポイントでピア キャッシュを使用し、クラウドのシナリオ向けに配信の最適化を使用します。

- 既存の BranchCache が実装されている:3 つのテクノロジすべてを並行して使用します。 BranchCache でサポートされていないシナリオでは、ピア キャッシュと配信の最適化を使用します。


## <a name="branchcache"></a>BranchCache

[BranchCache](/windows-server/networking/branchcache/branchcache) は Windows のテクノロジです。 BranchCache をサポートし、BranchCache 用に構成されている展開をダウンロードしたクライアントは、BranchCache が有効な他のクライアントに対するコンテンツ ソースとして機能します。  

たとえば、Windows Server 2012 以降を実行する配布ポイントがあり、BranchCache サーバーとして構成されているものとします。 最初の BranchCache が有効なクライアントがこのサーバーのコンテンツを要求すると、クライアントはそのコンテンツをダウンロードしてキャッシュします。  

- その後、このクライアントは、同じサブネット上にあって BranchCache が有効であり、やはりコンテンツをキャッシュするようになっている他のクライアントが、コンテンツを利用できるようにします。  
- 同じサブネット上の他のクライアントは、配布ポイントからコンテンツをダウンロードする必要はありません。  
- コンテンツは、今後の転送に備えて複数のクライアントに分散されます。  

詳細については、[Windows BranchCache のサポート](../configs/support-for-windows-features-and-networks.md#bkmk_branchcache)に関するページをご覧ください。

## <a name="delivery-optimization"></a>配信の最適化

<!-- 1324696 -->
Configuration Manager の境界グループを使って、企業ネットワークおよびリモート オフィスへのコンテンツ配布を定義して調整します。 [Windows の配信最適化](/windows/deployment/update/waas-delivery-optimization)は、Windows 10 デバイス間でコンテンツを共有するための、クラウド ベースのピア ツー ピア テクノロジです。 ピア間でコンテンツを共有するときは、境界グループの使用に向けて配信の最適化を構成します。 クライアントの設定は、クライアントでの配信最適化グループの識別子として境界グループ識別子を適用します。 クライアントは、配信の最適化クラウド サービスと通信するとき、この識別子を使ってコンテンツを含むピアを検索します。 詳細については、「[配信の最適化](../../clients/deploy/about-client-settings.md#delivery-optimization)」のクライアント設定をご覧ください。

配信の最適化は、Windows 10 品質更新プログラム用の高速インストール ファイルの Windows 10 更新プログラムの配信を最適化するために推奨されるテクノロジです。 Configuration Manager バージョン 1910 以降、配信の最適化クラウド サービスへのインターネット アクセスは、ピアツーピア機能を利用するための要件です。 必要なインターネット エンドポイントの詳細については、[配信の最適化に関してよく寄せられる質問](/windows/deployment/update/waas-delivery-optimization#frequently-asked-questions)に関するページをご覧ください。 最適化は、すべての Windows 更新プログラムに使用できます。 詳細については、[Windows 10 更新プログラムの配信の最適化](../../../sum/deploy-use/optimize-windows-10-update-delivery.md)に関するページをご覧ください。


## <a name="microsoft-connected-cache"></a>Microsoft 接続キャッシュ

<!--3555764-->
バージョン 1906 以降、配布ポイントに Microsoft 接続済みキャッシュ サーバーをインストールできます。 このコンテンツをオンプレミスでキャッシュすることによって、クライアントは配信の最適化機能から恩恵を受けることができますが、お客様が WAN リンクを保護するのに役立てることができます。

> [!NOTE]
> バージョン 1910 以降、この機能は、**Microsoft 接続済みキャッシュ**という名前になりました。 以前は、"配信の最適化のネットワーク内キャッシュ" という名前でした。

このキャッシュ サーバーは、配信の最適化によってダウンロードされたコンテンツに対して、オンデマンドの透過型キャッシュとして機能します。 クライアント設定を使用して、このサーバーがローカルの Configuration Manager 境界グループのメンバーにのみ提供されるようにします。

このキャッシュは、Configuration Manager の配布ポイントのコンテンツとは別のものです。 配布ポイントの役割と同じドライブを選択した場合は、コンテンツは個別に格納されます。

詳細については、「[Configuration Manager における Microsoft 接続済みキャッシュ](microsoft-connected-cache.md)」を参照してください。


## <a name="peer-cache"></a>ピア キャッシュ

クライアントのピア キャッシュは、リモートの場所にあるクライアントへのコンテンツの展開を管理するのに役立ちます。 ピア キャッシュとは、クライアントがローカル キャッシュで他のクライアントとコンテンツを直接共有できるようにするための組み込みの Configuration Manager ソリューションです。

まず、コレクションにピア キャッシュを有効にするクライアント設定を展開します。 これにより、そのコレクションのメンバーは、同じ境界グループ内の他のクライアント用のピア コンテンツ ソースとして機能できます。

バージョン 1806 以降では、クライアント ピア キャッシュ ソースのコンテンツを分割できます。 これにより、ネットワーク転送が最小限に抑えられ、WAN の使用率が減ります。 管理ポイントでは、コンテンツの各パートをより詳細に追跡することができます。 境界グループごとに同じ内容が複数回ダウンロードされないようにします。<!--1357346-->

詳細については、「[Configuration Manager クライアントのピア キャッシュ](client-peer-cache.md)」を参照してください。


## <a name="windows-pe-peer-cache"></a>Windows PE ピア キャッシュ

Configuration Manager で新しい OS を展開するとき、タスク シーケンスを実行しているコンピューターは Windows PE ピア キャッシュを使うことができます。 このようなコンピューターは、配布ポイントの代わりに、ピア キャッシュ ソースからコンテンツをダウンロードします。 この動作により、ローカル配布ポイントが存在しないブランチ オフィス シナリオで WAN のトラフィックが最小限に抑えられます。

詳細については、「[Windows PE ピア キャッシュ](../../../osd/get-started/prepare-windows-pe-peer-cache-to-reduce-wan-traffic.md)」を参照してください。


## <a name="windows-ledbat"></a>Windows LEDBAT

<!--1358112-->
Windows Low Extra Delay Background Transport (LEDBAT) は、バックグラウンド ネットワーク転送の管理に役立つ、Windows Server のネットワークの輻そう制御機能です。 サポートされている Windows Server のバージョンで実行される配布ポイントに対して、ネットワーク トラフィックの調整に役立つオプションを有効にします。 その後、クライアントでは利用可能な場合にのみ、ネットワーク帯域幅が使用されます。

一般的な Windows LEDBAT の詳細については、[新しいトランスポートの発展](https://techcommunity.microsoft.com/t5/Networking-Blog/Announcing-Transport-Features-and-Performance-Advancements-in/ba-p/339726)に関するブログ投稿を参照してください。

Configuration Manager 配布ポイントで Windows LEDBAT を使用する方法の詳細については、[配布ポイントの全般設定を構成する](../../servers/deploy/configure/install-and-configure-distribution-points.md#bkmk_config-general)際に **[Adjust the download speed to use the unused network bandwidth (Windows LEDBAT)]\(未使用のネットワーク帯域幅を使用するようにダウンロード速度を調整する (Windows LEDBAT)\)** の設定を確認してください。


## <a name="client-locations"></a>クライアントの場所

クライアントは、次の場所にあるコンテンツにアクセスします。  

- **イントラネット** (オンプレミス):  

    - 配布ポイントでは、HTTP または HTTPS を使用できます。  

    - オンプレミスの配布ポイントを使用できない場合、フォールバックとしてクラウドの配布ポイントのみを使用します。  

- **インターネット**:  

    - HTTPS を受け入れるには、インターネットに接続されている配布ポイントが必要です。  

    - クラウド配布ポイントまたはクラウド管理ゲートウェイ (CMG) を使用できます。  

        バージョン 1806 からは、CMG でクライアントにコンテンツを提供できるようにもなりました。 この機能により、Azure VM の必要な証明書とコストが削減されます。 詳細については、[CMG の変更](../../clients/manage/cmg/setup-cloud-management-gateway.md)に関するページを参照してください。

- **ワークグループ**:  

    - HTTPS を受け入れるには配布ポイントが必要です。  

    - クラウドの配布ポイントまたは CMG を使用できます。  


## <a name="content-source-priority"></a>コンテンツ ソースの優先順位

クライアントでコンテンツが必要な場合は、コンテンツの場所の要求を管理ポイントに対して行います。 管理ポイントからは、要求されたコンテンツで有効なソースの場所の一覧が返されます。 この一覧は、特定のシナリオ、使用されているテクノロジ、サイト設計、境界グループ、展開の設定によって異なります。 たとえば、タスク シーケンスが実行されるときに、完全な Configuration Manager クライアントが常に実行されているわけではないため、動作が異なる場合があります。<!-- SCCMDocs#1960 -->

次の一覧には、Configuration Manager クライアントで使用できる、利用可能なコンテンツ ソースの場所がすべて含まれており、優先順に示されています。  

1. クライアントと同じコンピューター上の配布ポイント
2. 同じネットワーク サブネット内のピア ソース
3. 同じネットワーク サブネット内の配布ポイント
4. 同じ境界グループ内のピア ソース
5. 現在の境界グループ内の配布ポイント
6. フォールバック用に構成された近隣境界グループ内の配布ポイント
7. 既定のサイト境界グループ内の配布ポイント
8. Windows Update クラウド サービス
9. インターネットに接続されている配布ポイント
10. Azure 内のクラウド配布ポイント

> [!Note]  
> <!-- SCCMDocs#1607 -->配信の最適化は、このソースの優先順位付けには適用されません。 この一覧は、Configuration Manager クライアントがコンテンツを検索する方法を示しています。 Windows Update エージェントは、配信の最適化のためにコンテンツをダウンロードします。 Windows Update エージェントがコンテンツを見つけられない場合、Configuration Manager クライアントはこの一覧を使用して検索します。

## <a name="content-library"></a>コンテンツ ライブラリ

コンテンツ ライブラリは、Configuration Manager でのコンテンツの単一インスタンス ストアです。 このライブラリにより、配布されるコンテンツの全体的なサイズが減ります。  

- [コンテンツ ライブラリ](the-content-library.md)の詳細を確認してください。
- コンテンツがもはやアプリケーションに関連していない場合は、[コンテンツ ライブラリのクリーンアップ ツール](content-library-cleanup-tool.md) を使用して、コンテンツを削除します。  


## <a name="distribution-points"></a>配布ポイント

Configuration Manager は、配布ポイントを使用して、クライアント コンピューターで実行するソフトウェアに必要なファイルを格納します。 クライアントは、展開するコンテンツのファイルがダウンロード可能な 1 つ以上の配布ポイントにアクセスできる必要があります。  

基本的な (特殊化されていない) 配布ポイントは、標準配布ポイントとも呼ばれます。 標準配布ポイントには次の 2 つのバリエーションがあることに注目してください。  

- **プル配布ポイント**:配布ポイントが他の配布ポイント (ソース配布ポイント) からコンテンツを取得する、配布ポイントのバリエーションの 1 つ。 このプロセスは、クライアントが配布ポイントからコンテンツをダウンロードするのと同じです。 プル配布ポイントを使用すると、サイト サーバーが各配布ポイントにコンテンツを直接配布する必要があるときに発生するネットワーク帯域幅のボトルネックを解消することができます。 [プル配布ポイントを使う](use-a-pull-distribution-point.md)。

- **クラウド配布ポイント**:Microsoft Azure にインストールされている配布ポイントのバリエーションの 1 つ。 クラウド配布ポイントの使い方については、[こちら](use-a-cloud-based-distribution-point.md)を参照してください。  

標準配布ポイントは、さまざまな構成と機能をサポートします。  

- この転送を制御するには、**スケジュール**や**帯域幅調整**などのコントロールを使います。  

- ネットワークの消費量を最小限にして制御するには、**事前設定されたコンテンツ**や**プル配布ポイント**などのコントロールを使います。  

- **BranchCache**、**ピア キャッシュ**、および**配信の最適化**は、コンテンツを展開するときに使われるネットワーク帯域幅を削減するためのピア ツー ピア テクノロジです。  

- OS の展開には、 **[PXE](../../../osd/get-started/prepare-site-system-roles-for-operating-system-deployments.md#BKMK_PXEDistributionPoint)** や **[マルチキャスト](../../../osd/get-started/prepare-site-system-roles-for-operating-system-deployments.md#BKMK_DPMulticast)** など、さまざまな構成があります  

- **モバイル デバイス**のオプション  
  
クラウドおよびプル配布ポイントでは、これらの同じ構成の多くがサポートされますが、各配布ポイントのバリエーションに固有の制限事項があります。  


## <a name="distribution-point-groups"></a>配布ポイント グループ

配布ポイント グループは、コンテンツ配布の簡素化に役立つ、配布ポイントの論理的グループです。  

詳細については、「[配布ポイント グループの管理](../../servers/deploy/configure/install-and-configure-distribution-points.md#bkmk_manage)」を参照してください。


## <a name="distribution-point-priority"></a>配布ポイントの優先順位

配布ポイントの優先順位値は、以前の展開をその配布ポイントに転送するのに要した時間に基づきます。  

- この値は、自動的に調整されます。 Configuration Manager がより多くの配布ポイントにいっそう速くコンテンツを転送できるように、各配布ポイントで設定されます。  

- 同時に複数の配布ポイントに、または配布ポイント グループに、コンテンツを配布するとき、サイトは優先順位が最も高いサーバーに最初にコンテンツを送信します。 その後、その同じコンテンツを優先順位の低い配布ポイントに送信します。  

- 配布ポイントの優先順位によって、パッケージ配布の優先順位が置き換わることはありません。 パッケージの優先順位は、サイトが異なるコンテンツを送信するときの決定因子のままです。  

たとえば、パッケージの優先順位が高いパッケージがあるものとします。 それを、配布ポイントの優先順位が低いサーバーに配布します。 この優先順位の高いパッケージは、常に、優先順位の低いパッケージより前に転送されます。 パッケージの優先順位は、サイトが低い優先順位のパッケージを配布ポイントの優先順位の高いサーバーに配布する場合であっても、適用されます。

つまり、Configuration Manager では、優先順位の高いパッケージのコンテンツは、優先順位の低いパッケージより前に配布ポイントに配布されることが保証されます。  

> [!NOTE]  
> また、プル配布ポイントは、優先順位の概念を使用して、ソース配布ポイントのシーケンスを決定しています。  
>
> - サーバーへのコンテンツ転送に対する配布ポイントの優先順位は、プル配布ポイントが使う優先順位とは異なります。 プル配布ポイントは、ソース配布ポイントからコンテンツを検索するとき、プル配布ポイントの優先順位を使います。  
> - 詳細については、「[プル配布ポイントの使用](use-a-pull-distribution-point.md)」をご覧ください。  


## <a name="fallback"></a>フォールバック

Configuration Manager の現在のブランチでは、クライアントによる (フォールバックを含めた) コンテンツを含む配布ポイントの検索方法について、いくつかのことが変更されています。

現在の境界グループに関連付けられた配布ポイントのコンテンツが見つからないクライアントは、近隣の境界グループに関連付けられたコンテンツ ソースの場所を使うようにフォールバックします。 フォールバックとして使用するには、近隣の境界グループにクライアントの現在の境界グループとの関係が定義されている必要があります。 このリレーションシップには、コンテンツがローカルで見つからないクライアントが検索により近隣の境界グループのコンテンツ ソースを含めることができるまでに経過しなければならない時間が構成されています。

優先配布ポイントの概念は使用されなくなりました。また、 **[代替のコンテンツ ソースの場所の使用を許可する]** の設定も使用または適用されなくなりました。

詳細については、「[境界グループ](../../servers/deploy/configure/boundary-groups.md)」を参照してください。


## <a name="network-bandwidth"></a>ネットワークの帯域幅

コンテンツを配布するときに使用されるネットワーク帯域幅を管理するために、次のオプションを使用できます。  

- **事前設定されたコンテンツ**:ネットワーク経由でコンテンツを配布せずに、配布ポイントにコンテンツを転送。  

- **スケジュールと調整**:配布ポイントにコンテンツを配布するタイミングと方法を制御するための構成。  

詳細については、「[ネットワーク帯域幅の管理](manage-network-bandwidth.md)」を参照してください。


## <a name="network-connection-speed-to-content-source"></a>コンテンツ ソースへのネットワーク接続の速度

Configuration Manager の現在のブランチでは、クライアントがコンテンツを含む配布ポイントを検索する方法について、いくつかのことが変更されています。 これらの変更には、コンテンツ ソースへのネットワーク速度が含まれます。

配布ポイントを **[高速]** または **[低速]** として定義するネットワーク接続速度は使用されなくなりました。 代わりに、境界グループに関連付けられている各サイト システムが同じように処理されます。

詳細については、「[境界グループ](../../servers/deploy/configure/boundary-groups.md)」を参照してください。


## <a name="on-demand-content-distribution"></a>オンデマンドのコンテンツ配布

オンデマンド コンテンツ配布は、個々のアプリケーションおよびパッケージの展開のオプションです。 このオプションは、優先サーバーへのオンデマンドのコンテンツ配布を有効にします。  

- 展開に対してこの設定を有効にするには、 **[このパッケージのコンテンツを優先配布ポイントに配布する]** を有効にします。  

- このオプションを展開に対して有効にしており、クライアントでコンテンツが要求されたものの、そのコンテンツがクライアントの優先配布ポイントのいずれかで利用できない場合、Configuration Manager によって、そのコンテンツが自動的にクライアントの優先配布ポイントに配布されます。  

- このオプションが有効になっていると、Configuration Manager によってクライアントの優先配布ポイントにコンテンツが自動的に配布されますが、クライアントの優先配布ポイントが展開を受け取る前にクライアントが他の配布ポイントからコンテンツを取得する可能性があります。 この動作が発生した場合、コンテンツは、その展開を必要とする他のクライアントで利用できるように、その配布ポイント上に保持されます。  

詳細については、「[境界グループ](../../servers/deploy/configure/boundary-groups.md)」を参照してください。


## <a name="package-transfer-manager"></a>パッケージ転送マネージャー

パッケージ転送マネージャーは、他のコンピューター上の配布ポイントにコンテンツを転送するサイト サーバー コンポーネントです。  

詳細については、「[パッケージ転送マネージャー](package-transfer-manager.md)」をご覧ください。  


## <a name="prestage-content"></a>コンテンツの事前設定

事前設定されたコンテンツは、ネットワーク経由でコンテンツを配布せずに、配布ポイントにコンテンツを転送するプロセスです。  

詳細については、「[ネットワーク帯域幅の管理](manage-network-bandwidth.md)」を参照してください。