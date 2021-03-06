---
title: Azure NetApp Files のボリュームのサービス レベルを動的に変更する | Microsoft Docs
description: ボリュームのサービス レベルを動的に変更する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: bd28f949d35d38c9e64af7ff4196aa1754fbc37a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172534"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>ボリュームのサービス レベルを動的に変更する

既存のボリュームのサービス レベルは変更できます。それには、目的のボリュームで使用したい[サービス レベル](azure-netapp-files-service-levels.md)を使用中の別の容量プールに、目的のボリュームを移動します。 ボリュームのサービス レベルのインプレース変更では、データを移行する必要はありません。 ボリュームへのアクセスにも影響はありません。  

> [!IMPORTANT] 
> この機能を使用するには、ホワイトリスト登録が必要です。 この機能を要求するには、お使いのサブスクリプション ID を記載したメールを anffeedback@microsoft.com までお送りください。

この機能により、オンデマンドでワークロードのニーズを満たすことができます。  既存のボリュームを変更し、パフォーマンス向上のために上位のサービス レベルを使用することも、コスト最適化のために下位のサービス レベルを使用することもできます。 たとえば、ボリュームが現在 *Standard* サービス レベルを使用している容量プールにあり、そのボリュームで *Premium* サービス レベルを使用する場合は、そのボリュームを、*Premium* サービス レベルを使用している容量プールに動的に移動できます。  

ボリュームの移動先になる容量プールは、既に存在している必要があります。 容量プールには、他のボリュームを含めることができます。  まったく新しい容量プールにボリュームを移動する場合は、ボリュームを移動する前に[容量プールを作成する](azure-netapp-files-set-up-capacity-pool.md)必要があります。  

## <a name="considerations"></a>考慮事項

* ボリュームを別の容量プールに移動した後は、以前のボリューム アクティビティ ログとボリューム メトリックにアクセスできなくなります。 ボリュームは、新しい容量プールのもとで新しいアクティビティ ログとメトリックを使用して開始されます。

* 上位のサービス レベルの容量プールにボリュームを移動する場合 (たとえば、*Standard* から *Premium* または *Ultra* サービス レベルに移動する) は、下位のサービス レベルの容量プールに再度ボリュームを移動する (たとえば、*Ultra* から *Premium* または *Standard* へ移動する) には、少なくとも 7 日待機する必要があります。  
同じサービス レベルまたは下位のサービス レベルの容量プールにボリュームを移動する場合、この待機期間は適用されません。

## <a name="steps"></a>手順

1.  [ボリューム] ページで、サービス レベルを変更するボリュームを右クリックします。 **[Change Pool] (プールの変更)** を選択します。

    ![ボリュームを右クリックする](../media/azure-netapp-files/right-click-volume.png)

2. [Change Pool] (プールの変更) ウィンドウで、ボリュームの移動先となる容量プールを選択します。 

    ![プールの変更](../media/azure-netapp-files/change-pool.png)

3.  **[OK]** をクリックします。


## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
