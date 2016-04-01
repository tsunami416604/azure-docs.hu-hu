<properties
    pageTitle="備份 Azure 虛擬機器 | Microsoft Azure"
    description="利用 Azure 虛擬機器備份的這些程序來探索、註冊及備份您的虛擬機器。"
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor=""
    keywords="virtual machine backup; back up virtual machine; backup and disaster recovery"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="11/17/2015"
    ms.author="trinadhk; aashishr; jimpark; markgal"/>


# 備份 Azure 虛擬機器
本文提供如何備份現有 Azure 虛擬機器 (VM)，以根據您的公司的備份和災害復原原則保護 VM 的程序。

首先，您必須注意幾件事，才能備份 Azure 虛擬機器。 如果您尚未這樣做，請完成 [必要條件](backup-azure-vms-prepare.md) 備妥環境來備份 Vm，再繼續進行。

如需詳細資訊，請參閱文件上 [規劃 VM 備份基礎結構在 Azure 中的](backup-azure-vms-introduction.md) 和 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。

備份 Azure 虛擬機器需要三個主要步驟：

![備份 Azure IaaS VM 的三個步驟](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE] 備份虛擬機器是本機的程序。 您無法將虛擬機器從一個區域備份到另一個區域中的備份保存庫。 因此，對於每一個有 VM 需要備份的 Azure 區域，必須在該區域中至少建立一個備份保存庫。

## 步驟 1 - 探索 Azure 虛擬機器
探索程序一律為首要執行步驟，以確保能夠識別任何加入至訂用帳戶的新虛擬機器。 此程序會在 Azure 中查詢訂用帳戶中的虛擬機器清單，以及其他資訊，例如雲端服務名稱、區域等。

1. 瀏覽至備份保存庫下 **復原服務** 在 Azure 入口網站，然後按一下 **註冊的項目**。

2. 選取 **Azure 虛擬機器** 從下拉式選單。

    ![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下 [ **探索** 頁面的底部。
    ![探索按鈕](./media/backup-azure-vms/discover-button-only.png)

    在列表顯示虛擬機器時，探索程序可能需花費幾分鐘的時間。 畫面底部會有通知讓您知道程序正在執行中。

    ![探索 VM](./media/backup-azure-vms/discovering-vms.png)

    程序完成時，通知隨即變更。

    ![探索完成](./media/backup-azure-vms/discovery-complete.png)

##  步驟 2 - 註冊 Azure 虛擬機器
您必須註冊 Azure 虛擬機器，使其與 Azure 備份服務相關聯。 這通常是一次性活動。

1. 瀏覽至備份保存庫下 **復原服務** 中 Azure 入口網站，然後按一下 **註冊的項目**。

2. 選取 **Azure 虛擬機器** 從下拉式選單。

    ![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下 [ **註冊** 頁面的底部。
    ![註冊按鈕](./media/backup-azure-vms/register-button-only.png)

4. 在 **註冊的項目** 快顯功能表上，選取您想要註冊的虛擬機器。 如果有兩個以上同名的虛擬機器，請使用雲端服務加以區別。

    >[AZURE.TIP] 可以一次登錄多個虛擬機器。

    系統會為您所選取的每個虛擬機器建立一個工作。

5. 按一下 [ **檢視作業** 中通知可移至 **工作** 頁面。

    ![註冊作業](./media/backup-azure-vms/register-create-job.png)

    虛擬機器也會連同註冊作業的狀態，出現在已註冊的項目清單中。

    ![註冊狀態 1](./media/backup-azure-vms/register-status01.png)

    當作業完成時，狀態會變更以反映 *註冊* 狀態。

    ![註冊狀態 2](./media/backup-azure-vms/register-status02.png)

## 步驟 3 - 保護 Azure 虛擬機器
現在，您可以設定虛擬機器的備份和保留原則。 使用單一保護動作可以保護多個虛擬機器。

2015 年 5 月之後建立的 Azure 備份保存庫，會隨附內建於保存庫的預設原則。 這項預設原則會隨附 30 天預設保留和每日一次的備份排程。

1. 瀏覽至備份保存庫下 **復原服務** 中 Azure 入口網站，然後按一下 **註冊的項目**。
2. 選取 **Azure 虛擬機器** 從下拉式選單。

    ![在入口網站中選取工作負載](./media/backup-azure-vms/select-workload.png)

3. 按一下 [ **保護** 頁面的底部。

     **保護項目精靈** 隨即出現。 此精靈只會列出已註冊且不受保護的虛擬機器。 您可以在此處選取要保護的虛擬機器。

    如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。

    >[AZURE.TIP] 您可以一次保護多個虛擬機器。

    ![設定大規模保護](./media/backup-azure-vms/protect-at-scale.png)

4. 選擇 **備份排程** 來備份您所選取虛擬機器。 您可以從現有的一組原則中挑選，或定義新的原則。

    每一個備份原則可以有多個相關聯的虛擬機器。 但無論何時，虛擬機器只能與一個原則相關聯。

    ![使用新的原則來保護](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] 備份原則包括排程備份的保留配置。 如果您選取現有的備份原則，將無法在下一個步驟中修改保留選項。

5. 選擇 **保留範圍** 與備份相關聯。

    ![使用彈性保留來保護](./media/backup-azure-vms/policy-retention.png)

    保留期原則會指定儲存備份的時間長度。 您可以根據進行備份的時間指定不同的保留原則。 例如，為了稽核目的，每一季結尾所進行的備份可能需要保留較長期間，而每日進行的備份 (可充當作業的復原點) 則只需要保留 90 天。

    ![搭配復原點備份虛擬機器](./media/backup-azure-vms/long-term-retention.png)

    在此範例影像中：

    - **每日的保留原則**︰ 每日備份會儲存 30 天。
    - **每週的保留原則**: 104 週的每週於星期日備份會保留。
    - **每月的保留原則**︰ 每個月的最後一個星期日的備份會保留 120 個月。
    - **每年的保留原則**: 99 會保留每年 1 月的第一個星期日的備份。

    建立的工作可設定保護原則，並將虛擬機器與您所選取的每個虛擬機器的該項原則相關聯。

6. 按一下 [ **工作** 選擇正確的篩選器來檢視的清單 **設定保護** 工作。

    ![設定保護工作](./media/backup-azure-vms/protect-configureprotection.png)

## 初始備份
一旦虛擬機器受到原則，就會顯示在 **受保護項目** 狀態] 索引標籤 *（擱置中的初始備份） 的受保護的*。 根據預設，第一個排定的備份是 *初始備份*。

若要在設定保護之後立即觸發初始備份：

1. 按一下 [ **立即備份** 底部的按鈕 **受保護項目** 頁面。

    Azure 備份服務會初始備份作業建立備份工作。

2. 按一下 [ **工作** 索引標籤來檢視工作清單。

    ![備份進行中](./media/backup-azure-vms/protect-inprogress.png)

>[AZURE.NOTE] 備份作業的一部分，Azure 備份服務會發出命令給每個虛擬機器，以排清所有寫入並取得一致的快照備份的擴充功能。

當初始備份完成時，在虛擬機器的狀態 **受保護項目** ] 索引標籤會 *受保護*。

![搭配復原點備份虛擬機器](./media/backup-azure-vms/protect-backedupvm.png)

## 檢視備份狀態和詳細資料
受到保護後，虛擬機器計數也會增加在 **儀表板** 摘要頁面。  **儀表板** 頁面也會顯示已在過去 24 小時的工作數目 *成功*, ，有 *失敗*, ，並且仍 *進行*。 藉由按一下任何一個類別，您可以向下鑽研該分類中 **工作** 頁面。

![儀表板頁面中的備份狀態](./media/backup-azure-vms/dashboard-protectedvms.png)

儀表板中的值會每隔 24 小時重新整理一次。

## 錯誤疑難排解
如果虛擬機器執行備份時的問題，請查看本 [疑難排解指引](backup-azure-vms-troubleshoot.md) 的說明。

## 後續步驟

- [管理和監視虛擬機器](backup-azure-manage-vms.md)
- [還原虛擬機器](backup-azure-restore-vms.md)


