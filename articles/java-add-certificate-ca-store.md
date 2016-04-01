<properties 
    pageTitle="新增憑證至 Java CA 存放區 | Microsoft Azure" 
    description="了解如何將憑證授權單位 (CA) 憑證新增至 Twilio 服務或 Azure 服務匯流排的 Java CA 憑證 (cacerts) 存放區。" 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

# 新增憑證至 Java CA 憑證存放區
下列步驟顯示如何新增憑證授權單位 (CA) 憑證到 Java CA 憑證 (cacerts) 存放區。 使用的範例是針對 Twilio 服務所要求的 CA 憑證。 本主題稍後提供的資訊將說明如何安裝 Azure 服務匯流排的 CA 憑證。 

您可以使用 keytool 新增 CA 憑證，再將 JDK 壓縮並將它加入至您的 Azure 專案 **approot** 資料夾，或者也可以執行使用了 keytool 來新增憑證的 Azure 啟動工作。 這個範例假設您會先新增 CA 憑證再將 JDK 壓縮。 另外，範例中將使用特定 CA 憑證，但是若要取得不同的 CA 憑證並將之儲存到 cacerts 存放區，步驟會很類似。

## 新增憑證至 cacerts 存放區

1. 在命令提示字元設定為 JDK **jdk\jre\lib\security** 資料夾中，執行下列命令來查看已安裝哪些憑證 ︰

    `keytool -list -keystore cacerts`

    系統會提示您輸入存放區密碼。 預設密碼是 **changeit**。 (如果您想要變更密碼，請參閱 keytool 文件， <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>。)這個範例假設 MD5 指紋為 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 的憑證未列在其中，而您想要將它匯入 (Twilio API service 需要這個特定的憑證)。
2. 從列出的憑證清單取得憑證 [GeoTrust 根憑證](http://www.geotrust.com/resources/root-certificates/)。 以滑鼠右鍵按一下序號 35:DE:F4:CF 之憑證的連結，並將它儲存 **jdk\jre\lib\security** 資料夾。 此範例的目的，它儲存到名為 **Equifax\_Secure\_Certificate\_Authority.cer**。
3. 透過下列命令匯入憑證：

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    當系統提示您信任這個憑證，如果憑證有 MD5 指紋 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4，回應輸入 **y**。
4. 執行下列命令確定成功匯入 CA 憑證：

    `keytool -list -keystore cacerts`

5. JDK 壓縮並將它加入至您的 Azure 專案 **approot** 資料夾。

如需 keytool 的詳細資訊，請參閱 <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>。

## Azure 根憑證

使用 Azure 服務 (例如 Azure 服務匯流排) 的應用程式需要信任 Baltimore CyberTrust 根憑證。 (自 2013 年 4 月 15 日起，Azure 就已開始從 GTE CyberTrust 全域根憑證移轉到 Baltimore CyberTrust 根憑證。 此次移轉花費了幾個月的時間完成。)

Baltimore 憑證可能已經安裝在您的 cacerts 存放區，因此請記得執行 **keytool-清單** 命令，先以查看它是否已經存在。

如果您需要新增 Baltimore CyberTrust 根憑證，它的序號是 02:00:00:b9、SHA1 指紋是 d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74。 您可以從下載 <https://cacert.omniroot.com/bc2025.crt>, 儲存到本機檔案具有副檔名 **.cer**, ，然後再匯入使用 **keytool** 如上所示。

## 後續步驟

如需 Azure 所使用的根憑證的詳細資訊，請參閱 [Azure 根憑證移轉](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)。

如需 Java 的詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。


