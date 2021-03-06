---
title: ファイアウォールを使用する
titleSuffix: Azure Machine Learning
description: Azure Firewall を使用して Azure Machine Learning ワークスペースへのアクセスを制御します。 Azure Machine Learning が正常に機能するためにファイアウォールの通過を許可する必要があるホストについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 63e2ba93ecdc1131be6bd291fe436b42a2a2d19c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407032"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Azure Firewall の内側で Azure Machine Learning のワークスペースを使用する

この記事では、Azure Machine Learning ワークスペースで使用するために Azure Firewall を構成する方法について説明します。

Azure Firewall を使用して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御できます。 正しく構成しないと、ワークスペースを使用したときにファイアウォールが原因で問題が発生する可能性があります。 この記事で説明されているように、いずれも Azure Machine Learning ワークスペースで使用されるさまざまなホスト名があります。

## <a name="network-rules"></a>ネットワーク ルール

ファイアウォールで、この記事のアドレスとの間で送受信されるトラフィックを許可するネットワーク ルールを作成します。

> [!TIP]
> ネットワーク ルールを追加するときは、__プロトコル__を任意に設定し、ポートを `*` に設定します。
>
> Azure Firewall の構成について詳しくは、[Azure Firewall のデプロイと構成](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)に関する記事をご覧ください。

## <a name="microsoft-hosts"></a>Microsoft のホスト

このセクションのホストは Microsoft によって所有されており、ホストではワークスペースが適切に機能するために必要なサービスが提供されます。

| **ホスト名** | **目的** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | クラスターをトレーニングします |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Azure Machine Learning スタジオによって使用されます |
| **\*.azureml.ms** | Azure Machine Learning API によって使用されます |
| **\*.experiments.azureml.net** | Azure Machine Learning で実行される実験で使用されます |
| **\*.modelmanagement.azureml.net** | モデルを登録してデプロイするために使用されます|
| **mlworkspace.azure.ai** | ワークスペースを表示するときに Azure portal によって使用されます |
| **\*.aether.ms** | Azure Machine Learning パイプラインを実行するときに使用されます |
| **\*.instances.azureml.net** | Azure Machine Learning のコンピューティング インスタンスです |
| **\*.instances.azureml.ms** | ワークスペースで Private Link が有効な場合の Azure Machine Learning のコンピューティング インスタンスです |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | 基本 Docker イメージ用の Microsoft Container Registry |

## <a name="python-hosts"></a>Python のホスト

このセクションのホストは、Python パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。 

| **ホスト名** | **目的** |
| ---- | ---- |
| **anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
| **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
| **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |

## <a name="r-hosts"></a>R のホスト

このセクションのホストは、R パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。

> [!IMPORTANT]
> 内部的には、Azure Machine Learning 用の R SDK では Python パッケージが使用されます。 そのため、Python ホストにもファイアウォールの通過を許可する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN パッケージをインストールするときに使用されます。 |

次のステップ

* [Azure Firewall をデプロイして構成する](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 内で Azure ML の実験と推論のジョブを安全に実行する](how-to-enable-virtual-network.md)
