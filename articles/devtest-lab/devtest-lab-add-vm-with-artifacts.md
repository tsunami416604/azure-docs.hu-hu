    <properties
    pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
    description="Create a new virtual machine with Artifacts in DevTest Lab."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

# 將具有構件的 VM 加入 Azure 研測實驗室

## 概觀

您一開始可以使用 Azure 基本映像或使用您已上傳到實驗室的映像，在研測實驗室中建立虛擬機器。

DevTest 實驗室 *成品* 可讓您指定在建立 VM 時執行的動作。 構件動作可以執行諸如下列動作的程序：執行 Powershell 和 Bash 命令，以及安裝軟體。 成品 *參數* 可讓您自訂的成品進行特定案例。

您的實驗室包括來自公用研測實驗室構件儲存機制的構件，以及已建立並加入您自己之構件儲存機制的構件。

本文將示範如何使用構件，在實驗室中建立 VM。

## 加入具有構件的 VM

1. 登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 點選 **瀏覽**, ，然後點選 [ **DevTest 實驗室** 從清單中。

1. 從實驗室清單中，點選您想要在其中建立新的 VM 的實驗室。  

1. 在測試環境的刀鋒視窗中，點選 [ **+ 實驗室 VM** 如下圖所示。  
    ![DevTest 實驗室主要分頁](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. 在 **實驗室 VM** 刀鋒視窗中，輸入新的虛擬機器中的名稱 **實驗室 VM 名稱** 文字方塊。

1. 點選 **基底 / 設定必要的設定** 和選取 VM 基礎映像。

    ![實驗室 VM 設定](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)  

1. 選取基底的映像之後, **實驗室 VM** 刀鋒視窗會展開包括 **使用者名稱** 和 **密碼** 項目。 輸入 **使用者名稱** ，將會被授與虛擬機器上的系統管理員權限。  

    ![展開的實驗室 VM 刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. 輸入 **密碼**。

1. 點選 **VM 大小** ，然後選取其中一個預先定義的項目可指定處理器核心、 RAM 的大小和硬碟的大小來建立 VM。

1. 點選 **成品** ，從清單中的成品，選取並設定您想要加入至基底映像的成品。 **注意 ︰** 您是新手 DevTest 實驗室或設定成品，請跳至 [選取和設定成品](#configuring-an-artifact) 區段，然後再在完成後，回到這裡。

1. 點選 **建立** 將指定的 VM 加入至實驗室。

1. [實驗室] 刀鋒視窗會顯示 VM 的建立狀態做為第一個 **建立**, ，然後為 **執行** 已經啟動 VM。 若要連接至 VM，點選 [VM，從 VM 的分頁中，點選 **連接**。  

## 選取和設定構件

在建立 VM 時，您可以將成品新增依序點選 **成品** 從 **實驗室 VM** 刀鋒視窗。 這會顯示 **新增成品** 刀鋒視窗中，可讓您新增及設定您的 VM 成品從官方 DevTest 實驗室儲存機制 (**官方的儲存機制**) 和小組儲存機制中的成品。

![加入構件刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**將構件加入 VM**

針對您想要加入 VM 的每個構件，依照下列步驟執行：

1. 點選中所需的成品 **新增成品** ] 分頁，以檢視可讓您指定的成品參數刀鋒視窗。  

2. 輸入必要的參數值以及任何您所需的選用參數。  

3. 點選 **新增** 加入成品，並回到 **新增成品** 刀鋒視窗。

**變更構件的執行順序**

當您將 vm 設定成品時，顯示目前的成品數目的連結會顯示在頂端 **新增成品** 刀鋒視窗。 根據預設，構件的動作是依照它們加入 VM 的順序來執行。 若要變更成品執行的順序，只需拖放成品在清單中，並點選 **確定** 完成。  

**檢視/修改選取的構件**

請遵循下列步驟來檢視或修改所選取構件的參數：

1. 在頂端 **新增成品** 刀鋒視窗中，點選指出多少成品的連結已新增至 VM。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 若要檢視或編輯特定成品的參數，請點選該成品中的 **選取成品** 刀鋒視窗。  

1. 進行任何必要的變更，然後點選 **確定** 關閉 **新增成品** 刀鋒視窗。

1. 點選 **確定** 關閉 **選取成品** 刀鋒視窗。

1. 點選 **確定** 關閉 **新增成品** 刀鋒視窗。      

## 後續步驟

了解如何 [撰寫自訂的成品，您的 vm](devtest-lab-artifact-author.md)。


