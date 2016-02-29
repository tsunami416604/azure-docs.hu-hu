<properties 
    pageTitle="建立 Machine Learning 工作區 | Microsoft Azure" 
    description="如何建立 Azure Machine Learning Studio 的工作區" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/13/2015" 
    ms.author="garye;bradsev"/>


# 建立 Azure Machine Learning 工作區 

此功能表會連結至說明如何設定 Cortana 分析程序 (CAP) 所用的各種資料科學環境的主題。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

若要使用 Azure Machine Learning Studio，您需要有機器學習服務工作區。 此工作區包含您建立、管理及發行實驗所需的工具。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 建立工作區

1. 登入 Microsoft Azure 帳戶。
2. 在 Microsoft Azure 服務面板中，按一下 [ **MACHINE LEARNING**。

    ![機器學習服務][] 1

3. 按一下 [ **+ 新增** 視窗的底部。
4. 按一下 [ **DATA SERVICES**, ，然後 **機器學習**, ，然後 **快速建立**。

    ![快速建立新工作區][] 3

5. 輸入 **工作區名稱** 為您工作區，並指定 **工作區擁有者**。 工作區擁有者必須是有效的 Microsoft 帳戶 (例如 name@outlook.com)。

    > [AZURE.NOTE] 稍後，您可以共用您正在使用邀請他人到您的工作區的實驗。 您可以在 Machine Learning Studio 上 **設定** 頁面。 您只需要每個使用者的 Microsoft 帳戶或組織帳戶。

6. 指定 Azure **位置**, ，然後輸入現有的 Azure **儲存體帳戶** 或選取 **建立新的儲存體帳戶** 來建立新的。
7. 按一下 [ **建立 ML 工作區**。

您的機器學習工作區建立之後，您會看見它列在 **機器學習** 頁面。

管理您的工作區的相關資訊，請參閱 [Manage an Azure Machine Learning workspace]。
如果您遇到問題，建立您的工作區，請參閱 [Troubleshooting guide: Create and connect to an Machine Learning workspace]。

[Manage an Azure Machine Learning workspace]: machine-learning-manage-workspace.md
[Troubleshooting guide: Create and connect to an Machine Learning workspace]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->
 

