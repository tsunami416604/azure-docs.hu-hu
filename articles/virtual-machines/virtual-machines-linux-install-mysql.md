<properties
    pageTitle="設定 Linux VM 上的 MySQL | Microsoft Azure "
    description="了解如何在 Azure 中的 Linux 虛擬機器 (Ubuntu 或 RedHat 系列 OS) 上安裝 MySQL 堆疊"
    services="virtual-machines"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2015"
    ms.author="mingzhan"/>


#如何在 Azure 上安裝 MySQL

在本文中，您將學習如何安裝和執行 Linux 的 Azure 虛擬機器上設定 MySQL。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##Install MySQL 虛擬機器上

> [AZURE.NOTE] 您必須具備才能完成此教學課程中執行 Linux 的 Microsoft Azure 虛擬機器。 請參閱
[Azure Linux VM 教學課程](virtual-machines-linux-tutorial.md) 來建立和設定 Linux VM，使用 `mysqlnode` 做為 VM 名稱和 `azureuser` 身分後再繼續。

在此情況下，請使用連接埠 3306 作為 MySQL 連接埠。  

連接到您透過 putty 建立的 Linux VM。 如果這是第一次使用 Azure Linux VM，請參閱如何使用 putty 連接到 Linux VM [這裡](virtual-machines-linux-use-ssh-key.md)。

本文的範例將使用儲存機制封裝安裝 MySQL5.6。 事實上，MySQL5.6 的效能改良功能比 MySQL5.5 更多。  更多資訊 [這裡](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)。


若要在 Ubuntu 上安裝 MySQL5.6 ###How
以下範例將使用 Azure 中採用 Ubuntu 的 Linux VM。

步驟 1: 安裝 MySQL Server 5.6
    切換至 `root` 使用者:

           #[azureuser@mysqlnode:~]sudo-su

   安裝 mysql-server 5.6：

           #[root@mysqlnode ~] # 專門取得更新
           #[root@mysqlnode ~] # apt get y 安裝 mysql server 5.6

   在安裝期間，您會看到對話方塊視窗 poping 最多會要求您設定下列的 MySQL 根密碼，而且您需要在這裡設定密碼。

    ![映像](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p1.png)


    輸入密碼一次進行確認。

    ![映像](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p2.png)

-步驟 2: 登入 MySQL 伺服器

   MySQL Server 安裝完成之後，MySQL 服務便會自動啟動 。 您可以使用 `root` 使用者的身分登入 MySQL Server。
    使用下列命令以登入和輸入密碼。

             #[root@mysqlnode ~] # mysql uroot-p

步驟 3: 管理執行中的 MySQL 服務

   (a) 取得 MySQL 服務的狀態

            #[root@mysqlnode ~] # 服務 mysql 狀態

   (b) 啟動 MySQL 服務

            #[root@mysqlnode ~] # mysql 啟動服務

   (b) 停止 MySQL 服務

            #[root@mysqlnode ~] # 服務 mysql 停止

   (d) 重新啟動 MySQL 服務

            #[root@mysqlnode ~] # 服務 mysql 會重新啟動


###How Red Hat 作業系統系列，例如 CentOS、 Oracle Linux 上安裝 MySQL
以下範例將使用執行 CentOS 或 Oracle Linux 的 Linux VM。

步驟 1: 新增 MySQL Yum 儲存機制
   切換至 `root` 使用者:

           #[azureuser@mysqlnode:~]sudo-su

   下載並安裝 MySQL 發行套件：

           #[root@mysqlnode ~] # wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
           #[root@mysqlnode ~] # yum localinstall-y mysql-社群-版本-el6-5.noarch.rpm

步驟 2: 編輯檔案來啟用 MySQL 儲存機制下載 MySQL5.6 套件之下。

            #[root@mysqlnode ~] # vim /etc/yum.repos.d/mysql-community.repo

   將此檔案的每個值更新為下列值：

       \ # *啟用使用 MySQL 5.6*

        [mysql56 社群]
       名稱 = MySQL 5.6 Community Server

       baseurl = http://repo.mysql.com/yum/mysql-5.6-community/el/6/$ basearch /

       enabled=1

       gpgcheck=1

       gpgkey = 檔案: / 等/pki/rpm-gpg/RPM-GPG-金鑰-mysql

從 MySQL 儲存機制的步驟 3: 安裝 MySQL
   安裝 MySQL:

          #[root@mysqlnode ~] #yum 安裝 mysql community 伺服器

   將安裝 MySQL RPM 封裝和所有相關的封裝。

步驟 4: 管理執行中的 MySQL 服務

    (a) 檢查 MySQL 伺服器服務狀態:

          #[root@mysqlnode ~] #service mysqld 狀態

   (b) 檢查是否正在執行的預設連接埠的 MySQL 伺服器:

          #[root@mysqlnode ~] #netstat – tunlp|grep 3306
    (c) 啟動 MySQL 伺服器:

          #[root@mysqlnode ~] #service mysqld 開始

   (d) 停止 MySQL 伺服器：

          #[root@mysqlnode ~] #service mysqld 停駐點

   (e) 將 MySQL 設為在啟動系統啟動：

          #[root@mysqlnode ~] 上的 #chkconfig mysqld


###How SUSE Linux 上安裝 MySQL
以下範例將使用執行 OpenSUSE 的 Linux VM。

步驟 1: 下載並安裝 MySQL Serverr
    切換至 `root` 使用者執行下列命令: 

          #sudo-su

   下載及安裝 MySQL Server 封裝：

          #[root@mysqlnode ~] # zypper 更新

          #[root@mysqlnode ~] # zypper 安裝 mysql server mysql 對內 mysql

步驟 2: 管理執行中的 MySQL 服務e
    (a) 檢查 MySQL 伺服器的狀態:

          #[root@mysqlnode ~] # rcmysql 狀態

   (b) 查看 MySQL 伺服器的預設連接埠：

          #[root@mysqlnode ~] # netstat – tunlp|grep 3306


   (c) 啟動 MySQL 伺服器：

          #[root@mysqlnode ~] # rcmysql 開始

   (d) 停止 MySQL 伺服器：

          #[root@mysqlnode ~] # rcmysql 停駐點

   (e) 將 MySQL 設為在啟動系統啟動：

          #[root@mysqlnode ~] # insserv mysql

# # # Next 步驟
尋找更多的使用量和 MySQL 的相關資訊 [這裡](https://www.mysql.com/)。.
