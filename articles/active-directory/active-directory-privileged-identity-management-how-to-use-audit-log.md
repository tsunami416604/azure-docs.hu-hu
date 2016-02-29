<properties
   pageTitle="Azure 特殊權限身分識別管理：如何使用稽核記錄"
   description="了解如何在 Azure 特殊權限身分識別管理擴充功能中使用稽核記錄。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure 特殊權限身分識別管理：如何使用稽核記錄

## 使用稽核記錄
您可以使用「特殊權限身分識別管理」稽核記錄，來查看指定期間內的所有使用者指派與啟用。

## 瀏覽到稽核記錄
您可以按一下 PIM 儀表板中的 [稽核記錄]，來存取稽核記錄。

## 稽核記錄圖表
您可以使用稽核記錄，在折線圖中檢視啟用總數、每天最大啟用數目，以及每天平均啟用數目。  如果稽核記錄中有一個以上的角色，您也可以依角色篩選資料。

排序依據時間、 動作或角色使用 **時間**, ，**動作** 或 **角色** 按鈕。

## 稽核記錄清單
稽核記錄清單中的欄如下：

- **要求者** -要求角色啟用的使用者。
- **使用者** -啟用角色的使用者。
- **角色** -指派給使用者的角色。
- **動作** -與角色/使用者所採取的動作。
- **時間** -動作發生時。
- **推論** -如果任何文字輸入 [原因] 欄位在啟動過程中，它會出現。
- **到期** -如果使用者具有該角色永久有效年份 9999。

## 篩選稽核記錄

您也可以篩選的資訊顯示在稽核記錄檔，即可 **篩選** ] 按鈕。   **更新圖表參數分頁** 會出現。

### 變更日期範圍
選取其中一個變更的稽核記錄檔的時間範圍 **今天**, ，**過去一週**, ，**過去一個月**, ，或 **自訂** 按鈕。
當您選擇 **自訂** ] 按鈕，您會獲得 **從** 日期欄位和 **至** 想記錄的日期欄位來指定日期範圍。  您可以以 MM/DD/YYYY 格式輸入日期，或按一下 **行事曆** 圖示，並從行事曆選擇日期。

### 變更記錄中包含的角色

選取或取消選取 **角色** 您想要每個角色旁的核取方塊中加入或排除在記錄檔。

為稽核記錄設定所有篩選之後，按一下更新來篩選記錄中的資料。  如果資料不會立即出現，按一下 [ **重新整理** ] 按鈕。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

