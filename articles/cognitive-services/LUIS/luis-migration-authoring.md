---
title: Azure リソース オーサリング キーに移行する
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) オーサリング認証を電子メール アカウントから Azure リソースに移行する方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: cc14f1cd60f048ba01060b9ebdbca434af6b9751
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145627"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure リソース オーサリング キーに移行する

Language Understanding (LUIS) のオーサリング認証が、メール アカウントから Azure リソースに変更されました。 Azure リソースへの切り替えは現在、必須ではありませんが、今後、強制となる予定です。


## <a name="what-is-migration"></a>移行とは。

移行とは、オーサリング認証をメール アカウントから Azure リソースに変更するプロセスです。 移行後、お使いのアカウントは Azure サブスクリプションと Azure オーサリング リソースにリンクされます。 *すべての LUIS ユーザー (所有者またはコラボレーター) は、最終的に移行する必要があります。*

移行は、LUIS ポータルから実行する必要があります。 たとえば、LUIS CLI を使用してオーサリング キーを作成する場合、移行プロセスは LUIS ポータルで完了する必要があります。 移行後もアプリケーションに共同作成者を指定することはできますが、これらはアプリケーション レベルではなく Azure リソース レベルで追加されます。

> [!Note]
> 移行前は、共同作成者は LUIS アプリ レベルで "_コラボレーター_" と呼ばれます。 移行後は、同じ機能に対して、Azure リソース レベルで "_共同作成者_" の Azure ロールが使用されます。

## <a name="note-before-you-migrate"></a>移行前の注意事項

* 移行は一方向のプロセスです。 移行後に元に戻すことはできません。
* アプリケーションの所有者である場合、アプリケーションは一緒に自動的に移行されます。
* 所有者は移行するアプリのサブセットを選択できません。また、プロセスを元に戻すことはできません。
* 所有者が移行すると、アプリケーションはコラボレーター側からは削除されます。
* 所有者は、移行を通知するために、コラボレーターにメールを送信するよう求められます。
* アプリケーションのコラボレーターである場合、アプリケーションは一緒に移行されません。
* コラボレーターが移行したことを、所有者が知る方法はありません。
* 移行によって自動的にコラボレーターが収集され、Azure オーサリング リソースに移動または追加されることはありません。 アプリの所有者が、移行後にこの手順を実行する必要があります。 この手順では、[Azure オーサリング リソースへのアクセス許可](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)が必要です。
* Azure リソースに割り当てられたコラボレーターがアプリケーションにアクセスするには、移行が必要です。 そうしないと、アプリケーションを作成するためのアクセス権が付与されません。
* 移行されたユーザーをアプリケーションのコラボレーターとして追加することはできません。
* 別のユーザーが所有するアプリケーションに割り当てられている予測キーを所有している場合、これは所有者とコラボレーターの両方の移行をブロックします。 この記事で後述する推奨事項を参照してください。

> [!Note]
> 予測ランタイム リソースを作成する必要がある場合は、それを作成するための[別のプロセス](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)があります。

## <a name="migration-prerequisites"></a>移行の前提条件

* 有効な Azure サブスクリプションに関連付けられている必要があります。 サブスクリプションに追加してもらうようテナント管理者に依頼するか、[無料でサインアップ](https://azure.microsoft.com/free/)してください。
* LUIS ポータルまたは Azure portal から LUIS Azure オーサリング リソースを作成する必要があります。 LUIS ポータルからオーサリング リソースを作成することは、次のセクションで説明する移行フローの一部です。
* アプリケーションのコラボレーターの場合、アプリケーションは自動的には移行されません。 それらのアプリケーションは、エクスポートするか[エクスポート API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) を使用してバックアップすることをお勧めします。 移行後に、アプリを LUIS に再度インポートすることができます。 インポート プロセスにより、新しいアプリ ID を使用して新しいアプリが作成され、自身がその所有者になります。
* アプリケーションの所有者である場合、アプリは自動的に移行されるため、これらをエクスポートする必要はありません。 各アプリのコラボレーターのリストを保存することをお勧めします。 このリストが含まれているメール テンプレートは、必要に応じて、移行プロセスの一部として提供されます。


|ポータル|目的|
|--|--|
|[Azure](https://azure.microsoft.com/free/)| 予測リソースとオーサリング リソースを作成します。<br> リソースに共同作成者を割り当てます。|
|[LUIS](https://www.luis.ai)| 新しいオーサリング リソースに移行します。<br> 移行フローで新しいオーサリング リソースを作成します。<br> **[管理]**  >  **[Azure リソース]** ページで、アプリに予測リソースとオーサリング リソースを割り当てるか、アプリからそれらの割り当てを解除します。 <br> 1 つのオーサリング リソースから別のオーサリング リソースにアプリケーションを移動します。  |

> [!Note]
> LUIS アプリの作成は無料 (F0 レベル) です。 価格レベルの詳細については[こちら](luis-limits.md#key-limits)をご覧ください。


## <a name="migration-steps"></a>移行の手順

1. 作業している LUIS ポータルで、上部のツール バーの **Azure** アイコンから移行プロセスを開始できます。

   > [!div class="mx-imgBorder"]
   > ![移行アイコン](./media/migrate-authoring-key/migration-button.png)

2. 移行のポップアップ ウィンドウでは、移行を続行するか、後で移行するかを選択できます。 **[Migrate now]\(今すぐ移行\)** を選択します。

   > [!div class="mx-imgBorder"]
   > ![移行プロセスの最初のポップアップ ウィンドウで、[今すぐ移行する] を選択する](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. いずれかのアプリにコラボレーターがいる場合は、移行について通知するメールを送信するよう求められます。 これは省略可能な手順です。

   コラボレーターとアプリごとに、既定のメール アプリケーションが開き、簡単な書式が設定されたメールが表示されます。 このメールは送信前に編集できます。 メール テンプレートには、正確なアプリ ID とアプリ名が含まれています。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```

   > [!Note]
   > アカウントを Azure に移行すると、コラボレーターはアプリを利用できなくなります。

4. いずれかのアプリケーションのコラボレーターである場合は、移行フロー中にこのオプションを選択してアプリのコピーをエクスポートするように求められます。 これは省略可能な手順です。

   このオプションを選択すると、次のページが表示されます。 左側の [ダウンロード] ボタンを選択すると、目的のアプリがエクスポートされます。 これらのアプリは、自動的には移行されないため、移行後に再びインポートすることができます。

   > [!div class="mx-imgBorder"]
   > ![アプリケーションのエクスポートを確認するメッセージ。](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Azure で既に作成してある場合は、新しい LUIS オーサリング リソースを作成するか、既存のオーサリング リソースに移行することができます。 次のいずれかのボタンをクリックして、必要なオプションを選択します。

   > [!div class="mx-imgBorder"]
   > ![新しいオーサリング リソースを作成するためのボタンと既存のオーサリング リソースを使用するためのボタン](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>LUIS から新しいオーサリング リソースを作成して移行する

新しいオーサリング リソースを作成する場合は、 **[新しいオーサリング リソースを作成する]** を選択し、次のウィンドウで以下の情報を入力します。 **[完了]** を選択します。

> [!div class="mx-imgBorder"]
> ![オーサリング リソースを作成するためのウィンドウ](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **[テナント名]** : Azure サブスクリプションが関連付けられているテナント。 これは既定では、現在使用しているテナントに設定されます。 イニシャルを含む右端のアバターを選択して、テナントを切り替えることができます。
* **[リソース名]** :選択したカスタム名。 オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。
* **[サブスクリプション名]** : リソースに関連付けられるサブスクリプション。 テナントに属しているサブスクリプションが複数ある場合は、ドロップダウン リストから目的のサブスクリプションを選択します。
* **[Azure リソース グループ名]** : ドロップダウン リストから選択したカスタム リソース グループの名前。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。

1 つのサブスクリプションにつき、リージョンごとに 10 個の無料オーサリング リソースを使用できます。 サブスクリプションの同じリージョンに 10 個以上のオーサリング リソースがある場合、新しいリソースを作成することはできません。

オーサリング リソースが作成されると、成功メッセージが表示されます。 **[Close]\(閉じる\)** を選択してポップアップ ウィンドウを閉じます。

  > [!div class="mx-imgBorder"]
  > ![オーサリング リソースが正常に作成されたことを示すメッセージ](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>既存のオーサリング リソースを使用して移行する

サブスクリプションが LUIS オーサリング Azure リソースに既に関連付けられている場合、または Azure portal からリソースを作成済みであり、新しいものを作成するのではなく移行したい場合は、 **[既存のオーサリング リソースを使用する]** を選択します。 後続のウィンドウに次の情報を入力し、 **[完了]** を選択します。

> [!div class="mx-imgBorder"]
>![既存のオーサリング リソースを変更するためのウィンドウ](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **[テナント名]** : Azure サブスクリプションが関連付けられているテナント。 これは既定では、現在使用しているテナントに設定されます。
* **[サブスクリプション名]** : リソースに関連付けられるサブスクリプション。 テナントに属しているサブスクリプションが複数ある場合は、ドロップダウン リストから目的のサブスクリプションを選択します。
* **[リソース名]** : 移行先にするオーサリング リソース。

> [!Note]
> ドロップダウン リストに目的のオーサリング リソースが表示されない場合は、サインインしている LUIS ポータルに従って、適切な場所に作成されていることを確認してください。 また、実際に作成したものが、予測リソースではなく、オーサリング リソースであることを確認します。


オーサリング リソース名を確認し、 **[移行]** ボタンを選択します。

> [!div class="mx-imgBorder"]
> ![([移行] ボタンが選択できる状態になった) オーサリング リソースを選択するためのウィンドウ](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

成功メッセージが表示されます。 **[Close]\(閉じる\)** を選択してポップアップ ウィンドウを閉じます。

> [!div class="mx-imgBorder"]
> ![オーサリング リソースが正常に移行されたことを示すメッセージ](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>移行後のアプリの使用

移行プロセスが完了すると、自分が所有しているすべての LUIS アプリが、1 つの LUIS オーサリング リソースに割り当てられるようになります。

**[マイ アプリ]** の一覧に、新しいオーサリング リソースに移行されたアプリが表示されます。 アプリにアクセスする前に、サブスクリプションと LUIS オーサリング リソースを選択して、自分が作成できるアプリを確認します。

 > [!div class="mx-imgBorder"]
 > ![サブスクリプションとオーサリング リソースのボックス](./media/create-app-in-portal-select-subscription-luis-resource.png)

LUIS ポータルでアプリの編集を続けるために、オーサリング リソースのキーの情報は必要ありません。

プログラムによってアプリを編集する場合は、オーサリング キーの値が必要です。 これらの値は、LUIS ポータルの **[管理]**  >  **[Azure リソース]** ページに表示されます。 これらは、Azure portal のそのリソースの **[キー]** ページでも確認できます。 さらに多くのオーサリング リソースを作成し、同じページから割り当てることもできます。

 > [!div class="mx-imgBorder"]
 > ![オーサリング リソースを管理するためのページ](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>オーサリング リソースへの共同作成者の追加

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

オーサリング リソースに[共同作成者を追加する方法](luis-how-to-collaborate.md)について説明します。 共同作成者は、そのリソースのすべてのアプリケーションにアクセスできます。

**Azure portal** のオーサリング リソースの [Access Control (IAM)] ページから、そのリソースの共同作成者を追加できます。 詳細については、[共同作成者アクセスの追加](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)に関するページを参照してください。

> [!Note]
> LUIS アプリの所有者が移行し、Azure リソースの共同作成者としてコラボレーターを追加した場合、コラボレーターも移行しないと、アプリにアクセスできません。

## <a name="luis-portal-migration-reminders"></a>LUIS ポータルの移行リマインダー

移行プロセスは [LUIS ポータル](https://www.luis.ai)に用意されています。

これらの条件が両方とも該当する場合、移行するように求められます。
* オーサリングするアプリがメール認証システム上にある。
* アプリの所有者である。

毎週、アプリを移行するかどうかを確認するメッセージが表示されます。 移行せずにこのウィンドウを閉じることもできます。 次にスケジュールされている期間より前に移行する場合は、LUIS ポータル上部のツール バーにある **Azure** アイコンから移行プロセスを開始できます。

## <a name="prediction-resources-blocking-migration"></a>移行をブロックする予測リソース
移行はアプリケーションのランタイムに悪影響を及ぼします。 移行すると、すべてのコラボレーターがアプリから削除されます。また、他のアプリのコラボレーターの場合はそのアプリから削除されます。 このプロセスは、コラボレーターが割り当てたキーも削除されることを意味します。これにより、運用環境にあるアプリケーションが中断される可能性があります。 これが、割り当てられたコラボレーターまたはキーを手動で削除するまで、移行がブロックされる理由です。

これらのいずれかの条件に該当する場合、移行はブロックされます。

* 所有していないアプリで予測リソースまたはランタイム リソースを割り当てた。
* 所有しているアプリに他のユーザーが予測リソースまたはランタイム リソースを割り当てた。

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>アプリの所有者である場合に推奨される手順
一部のアプリケーションの所有者であり、コラボレーターによってこれらのアプリケーションに予測キーまたはランタイム キーが割り当てられている場合、移行するとエラーが表示されます。 エラーには、他のユーザーが所有する予測キーが割り当てられているアプリケーション ID が一覧表示されます。

推奨事項は次のとおりです。
* 移行についてコラボレーターに通知します。
* エラーに示されているアプリケーションからすべてのコラボレーターを削除します。
* 移行プロセスを実行します。コラボレーターを手動で削除した場合は、成功するはずです。
* コラボレーターを新しいオーサリング リソースに共同作成者として割り当てます。 コラボレーターは、予測リソースを移行し、アプリケーションに再度割り当てます。 これにより、予測リソースが再度割り当てられるまで、アプリケーションが一時的に中断されることに注意してください。

ここで、もう 1 つの解決策が考えられます。 所有者を移行する前に、コラボレーターが Azure portal から Azure サブスクリプションの共同作成者としてアプリの所有者を追加することです。 この手順により、所有者にランタイム予測リソースへのアクセス権が付与されます。 (新しいテナントの下にある) 追加された新しいサブスクリプションを使用して所有者が移行されると、コラボレーターとアプリ所有者の両方について移行プロセスのブロックが解除されるだけではありません。 予測キーを割り当てたまま、アプリを中断せずにアプリをスムーズに移行することもできます。


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>アプリのコラボレーターである場合に推奨される手順
アプリケーションで共同作業を行っており、これらのアプリケーションに予測キーまたはランタイム キーが割り当てられている場合、移行するとエラーが表示されます。 エラーには、移行をブロックしているアプリケーション ID とキー パスが一覧表示されます。

推奨事項は次のとおりです。
* アプリケーションをバックアップとしてエクスポートします。 これは、移行プロセスにおけるオプションの手順です。
* **[管理]**  >  **[Azure リソース]** ページから予測リソースの割り当てを解除します。
* 移行プロセスを実行します。
* 移行後にアプリケーションを再度インポートします。
* **[管理]**  >  **[Azure リソース]** ページから、アプリケーションに予測キーを再度割り当てます。

> [!Note]
> 移行後にアプリケーションを再度インポートすると、異なるアプリ ID が割り当てられます。 これらは、運用環境でヒットするものとは異なります。 これで、これらのアプリケーションの所有者になります。

## <a name="troubleshooting-the-migration-process"></a>移行プロセスに関するトラブルシューティング

移行しようとしても、ドロップダウン リストに Azure サブスクリプションが見つからない場合:
* Cognitive Services リソースの作成が承認されている有効な Azure サブスクリプションがあることを確認します。 [Azure portal](https://ms.portal.azure.com) にアクセスして、サブスクリプションの状態を確認します。 お持ちでない場合は、[無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
* 有効なサブスクリプションに関連付けられている、適切なテナント内であることを確認します。 このツール バーのイニシャルの左側にあるアバターからテナントを切り替えることができます。![テナントを切り替えることのできるツール バー](./media/migrate-authoring-key/switch-user-tenant-2.png)

既存のオーサリング リソースがあるのに、 **[既存のオーサリング リソースを使用する]** オプションを選択しても見つからない場合:
* お客様のリソースはおそらく、サインインしているポータルとは異なる場所に作成されています。 [LUIS のオーサリング リージョンとポータル](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)に関するセクションを参照してください。
* 代わりに、LUIS ポータルから新しいリソースを作成します。

**[新しいオーサリング リソースを作成する]** オプションを選択したが、"Failed retrieving user's Azure information, retry again later" (ユーザーの Azure 情報を取得できませんでした。後でもう一度やり直してください) というエラー メッセージが表示されて移行が失敗した場合:
* サブスクリプションには、サブスクリプションあたりのリージョンごとに 10 個以上のオーサリング リソースを含めることができます。 この場合、新しいオーサリング リソースを作成することはできません。
* **[既存のオーサリング リソースを使用する]** オプションを選択し、ご利用のサブスクリプションにある既存のリソースのいずれかを選択して移行します。

次のエラーが表示された場合は、「[アプリの所有者である場合に推奨される手順](#recommended-steps-if-youre-the-owner-of-the-app)」を参照してください。
![所有者に関して移行に失敗したことを示すエラー](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

次のエラーが表示された場合は、「[アプリのコラボレーターである場合に推奨される手順](#recommended-steps-if-youre-a-collaborator-on-an-app)」を参照してください。
![コラボレーターに関して移行に失敗したことを示すエラー](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>次のステップ

* [オーサリング キーとランタイム キーの概念](luis-how-to-azure-subscription.md)について見直す。
* [キーを割り当てる](luis-how-to-azure-subscription.md)方法と[共同作成者を追加](luis-how-to-collaborate.md)する方法について見直す。
