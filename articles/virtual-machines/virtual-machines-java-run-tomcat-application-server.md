<properties
    pageTitle="虛擬機器上的 Tomcat | Microsoft Azure"
    description="本教學課程使用的傳統部署模型，以建立資源，並示範如何建立 Windows 虛擬機器，並將它設定為執行 Apache Tomcat 應用程式伺服器。"
    services="virtual-machines"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe"
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="robmcm"/>

# 如何在以傳統部署模型建立的虛擬機器上執行 Java 應用程式伺服器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


Azure 可讓您利用虛擬機器來提供伺服器功能。 例如，於 Azure 上執行的虛擬機器在經過設定後可代管 Apache Tomcat 等 Java 應用程式伺服器。 完成本指南的內容後，您將了解如何建立於 Azure 上執行的虛擬機器，以及該如何設定才能執行 Java 應用程式伺服器。

您將了解：

* 如何建立已安裝 Java Development Kit (JDK) 的虛擬機器。
* 如何從遠端登入虛擬機器。
* 如何將 Java 應用程式伺服器安裝在虛擬機器上。
* 如何為虛擬機器建立端點。
* 如何在防火牆中為應用程式伺服器開啟連接埠。

本教學課程的目的是在虛擬機器上安裝 Apache Tomcat 應用程式伺服器。 完成安裝作業會產生如下的 Tomcat 安裝。

![執行 Apache Tomcat 的虛擬機器][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 建立虛擬機器

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [ **新增**, ，按一下 [ **計算**, ，按一下 [ **虛擬機器**, ，然後按一下 [ **從組件庫**。
3. 在 **虛擬機器映像選取** 對話方塊中，選取 **JDK 7 Windows Server 2012**。
請注意， **JDK 6 Windows Server 2012** 是若您已準備好在 JDK 7 中執行的舊版應用程式。
4. 按一下 [ **下一步**。
5. 在 [**虛擬機器組態**] 對話方塊中：
    1. 指定虛擬機器的名稱。
    2. 指定要用於虛擬機器的大小。
    3. 輸入系統管理員名稱 **使用者名稱** 欄位。 請記住您即將輸入的名稱和密碼，因為當從遠端登入此虛擬機器時將會需要用到。
    4. 中輸入密碼 **新密碼** 欄位，然後重新輸入在 **確認** 欄位。 此為系統管理員帳戶的密碼。
    5. 按一下 [ **下一步**。
6. 在下一個 [**虛擬機器組態**] 對話方塊中：
    1. 如 **雲端服務**, ，使用預設 **建立新的雲端服務**。
    2. 值 **雲端服務 DNS 名稱** 必須是唯一的 cloudapp.net。 必要時請修改此值，使 Azure 指出該值是唯一的。
    2. 指定區域、同質群組或虛擬網路。 基於本教學課程的目的，指定區域例如 **美國西部**。
    2. 如 **儲存體帳戶**, ，請選取 **使用自動產生的儲存體帳戶**。
    3. 如 **可用性設定組**, ，請選取 **(無)**。
    4. 按一下 [ **下一步**。
7. 在最終的 [**虛擬機器組態**] 對話方塊中：
    1. 接受預設的端點項目。
    2. 按一下 [ **完整**。

## 從遠端登入虛擬機器

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [ **虛擬機器**。
3. 按一下要登入的虛擬機器名稱。
4. 在啟動虛擬機器後，頁面底部的快顯功能表可允許連接。
5. 按一下 [ **連接**。
6. 視需要回應提示以連接虛擬機器。 這應會需要儲存或開啟包含連線詳細資料的 .rdp 檔案。 您可能必須將 url:port 複製為 .rdp 檔案中第一行的最後一個部分，並將其貼入遠端登入應用程式中。

## 將 Java 應用程式伺服器安裝在虛擬機器上

您可以將 Java 應用程式伺服器複製到虛擬機器，或您也可以透過安裝程式安裝 Java 應用程式伺服器。

基於本教學課程的目的，我們將安裝 Tomcat。

1. 當您登入您的虛擬機器時，開啟的瀏覽器工作階段 [Apache Tomcat](http://tomcat.apache.org/download-70.cgi)。
2. 按兩下連結 **32 位元/64 位元 Windows Service Installer**。 在使用本技術的前提下，Tomcat 將以 Windows 服務的形式安裝。
3. 出現提示時，選擇執行安裝程式。
4. 內 **Apache Tomcat Setup** 精靈，請依照提示安裝 Tomcat。 基於本教學課程的目的，接受預設值即可。 確定您已到達 **完成 Apache Tomcat Setup Wizard** 對話方塊中，您可以選擇性地選取 **執行 Apache Tomcat** 使 Tomcat 立即啟動。 按一下 [ **完成** 完成 Tomcat 安裝程序。

## 啟動 Tomcat
如果您沒有選擇執行 Tomcat **完成 Apache Tomcat Setup Wizard** 對話方塊方塊中，開啟您的虛擬機器上的命令提示字元並執行 **網路啟動 Tomcat7**。

您現在應該會看到執行中的 Tomcat 如果您執行虛擬機器的瀏覽器並開啟 <http://localhost:8080>。

若要從外部機器看見執行中的 Tomcat，您需要建立端點並開啟連接埠。

## 為虛擬機器建立端點
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [ **虛擬機器**。
3. 按一下執行 Java 應用程式伺服器之虛擬機器的名稱。
4. 按一下 [ **端點**。
5. 按一下 [ **新增**。
6. 在 **新增端點** 對話方塊方塊中，確認 **新增獨立端點** 已選取，然後按一下 [ **下一步**。
7. 在 [**新端點詳細資訊**] 對話方塊中：
    1. 指定端點的名稱例如， **HttpIn**。
    2. 指定 **TCP** 通訊協定。
    3. 指定 **80** 公用連接埠。
    4. 指定 **8080** 的私人連接埠。
    5. 按一下 [ **完成** ] 按鈕以關閉對話方塊。 系統隨即會建立端點。

## 在防火牆中為虛擬機器開啟連接埠
1. 登入虛擬機器。
2. 按一下 [ **視窗啟動**。
3. 按一下 [ **控制台**。
4. 按一下 [ **系統及安全性**, ，按一下 [ **Windows 防火牆**, ，然後按一下 [ **進階設定**。
5. 按一下 [ **輸入規則**, ，然後按一下 [ **新規則**。
 ![新增輸入的規則][NewIBRule]
6. 如 **規則類型**, ，請選取 **連接埠**, ，然後按一下 [ **下一步**。
 ![新增輸入的規則連接埠][NewRulePort]
7. 在 **通訊協定和連接埠** 畫面上，選取 **TCP**, ，指定 **8080** 為 **特定本機連接埠**, ，然後按一下 [ **下一步**。
 ![新增輸入的規則 ][NewRuleProtocol]
8. 在 **動作** 畫面上，選取 **允許連線**, ，然後按一下 [ **下一步**。
 ![新增輸入的規則動作][NewRuleAction]
9. 在 **設定檔** 畫面上，確定 **網域**, ，**私人**, ，和 **公用** 已選取，然後按一下 [ **下一步**。
 ![新增輸入的規則設定檔][NewRuleProfile]
10. 在 **名稱** 畫面上，指定規則的名稱，例如 **HttpIn** (規則名稱不一定要符合的端點名稱，不過)，然後按一下 [ **完成**。  
 ![新增輸入的規則名稱][NewRuleName]

此時，Tomcat 網站應該會從外部瀏覽器檢視使用下列形式的 URL **http://*your\_DNS\_name*。 cloudapp.net * *，其中 ***your\_DNS\_name*** 是您在建立虛擬機器時指定的 DNS 名稱。

## 應用程式生命週期考量
* 您可以建立您自己的 web 應用程式封存檔 (WAR)，將它加入至 **webapps** 資料夾。 例如，建立基本 Java Service Page (JSP) 動態 web 專案並將它匯出成 WAR 檔案，將 WAR 複製到 Apache Tomcat **webapps** 上虛擬機器，然後在瀏覽器中執行的資料夾。
* 依預設，當您安裝 Tomcat 服務時，系統會將其設定為手動啟動。 您可以使用 [服務] 嵌入式管理單元將其切換為自動啟動。 依序按一下 [服務] 嵌入式管理單元啟動 **Windows 開始**, ，**系統管理工具**, ，然後 **服務**。 按兩下 **Apache Tomcat** 服務和設定 **啟動類型** 至 **自動**。

    ![將服務設定為自動啟動][service_automatic_startup]

    使 Tomcat 自動啟動的優點在於，當虛擬機器重新開機時 (例如安裝需要重新開機的軟體更新後) 其將會啟動。

## 後續步驟
了解其他服務 (例如 Azure 儲存體、 服務匯流排和 SQL 資料庫)，您可能想要納入 Java 應用程式藉由檢視提供的資訊 [Java 開發人員中心](http://azure.microsoft.com/develop/java/)。

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

