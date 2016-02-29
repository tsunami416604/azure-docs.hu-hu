
本文章將說明如何在 Azure App Service 中設定 Web 應用程式的 HTTPS。 不包含用戶端憑證驗證。如需有關的資訊，請參閱 [方式來設定 TLS 相互驗證的 Web 應用程式](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md)。

根據預設，Azure 已啟用 HTTP 應用程式與 \*.azurewebsites.net 網域的萬用字元憑證。 如果您不打算設定自訂網域，您可以直接利用預設的 HTTPS 憑證。 不過，像是 [所有萬用字元網域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), ，不像使用自訂網域搭配自己的憑證那麼安全。   

這份文件的其餘部分提供詳細資料，例如自訂網域啟用 HTTPS **contoso.com**, ，**www.contoso.com**, ，或 **\*.contoso.com**

<a name="bkmk_domainname"></a>
## 為自訂網域啟用 SSL

若要啟用 HTTPS 的自訂網域，例如 **contoso.com**, ，您必須先 [Azure App Service 中設定自訂網域名稱](../articles/app-service-web/web-sites-custom-domain-name.md)。 然後執行下列動作：

1. [取得 SSL 憑證](#bkmk_getcert)
2. [設定標準定價層](#bkmk_standardmode)
2. [在應用程式中設定 SSL](#bkmk_configuressl)
3. [應用程式上強制 SSL](#bkmk_enforce) (選擇性)

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。 或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

<a name="bkmk_getcert"></a>
## 1.取得 SSL 憑證

在要求 SSL 憑證之前，您必須先決定有哪些網域名稱要由該憑證保護。 這將決定您必須取得的憑證類型。 如果您只需要保護單一網域名稱，例如 **contoso.com** 或 **www.contoso.com** 基本憑證就已足夠。 如果您需要保護多個網域名稱，例如 **contoso.com**, ，**www.contoso.com**, ，和 **mail.contoso.com**, ，則您可以取得 [萬用字元憑證](http://en.wikipedia.org/wiki/Wildcard_certificate), ，或是憑證 [主體替代名稱](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName)。

應用程式服務搭配使用的 SSL 憑證必須經過 [憑證授權單位](http://en.wikipedia.org/wiki/Certificate_authority) (CA)。 如果您還沒有此類憑證，則必須向發行 SSL 憑證的公司取得。 如需憑證授權單位的清單，請參閱 [Windows 和 Windows Phone 8 SSL 根憑證計劃 (成員 Ca)][cas] Microsoft TechNet Wiki 上。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
* 憑證的主體名稱必須和用來存取應用程式的網域相符。 如果您需要利用此憑證來供應多個網域，則必須如前文所討論，使用萬用字元值或指定 subjectAltName 值。
* 憑證至少應該要以 2048 位元加密。
* Azure App Service 不支援私人 CA 伺服器所核發的憑證。

若要取得可搭配 Azure App Service 使用的 SSL 憑證，您可將憑證簽署要求 (CSR) 提交至憑證授權單位，然後從您收回的憑證產生 .pfx 檔案。 您可以使用您所選的工具來執行此作業。 以下是取得憑證的一些常見方式：

- [使用 Certreq.exe 取得憑證](#bkmk_certreq)
- [使用 IIS 管理員取得憑證](#bkmk_iismgr)
- [使用 OpenSSL 取得憑證](#bkmk_openssl)
- [使用 OpenSSL 取得 SubjectAltName 憑證](#bkmk_subjectaltname)
- [產生自我簽署的憑證 (僅供測試)](#bkmk_selfsigned)

> [AZURE.NOTE] 下列步驟，當您將會提示您輸入 **一般名稱**, ，例如 `www.contoso.com`。 對於萬用字元憑證，這個值應該是 \*.domainname (例如，\*.contoso.com)。 如果您需要同時支援萬用字元名稱 (如 \*.contoso.com) 和根網域名稱 (如 contoso.com)，則可以使用萬用字元 subjectAltName 憑證。
>
> Azure App Service 支援橢圓曲線密碼編譯 (ECC) 憑證；不過，它們相對而言比較新，您應該在具體步驟中使用 CA 來建立 CSR。

您可能還需要取得 **[中繼憑證](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (也稱為鏈結憑證)，如果您的 CA 會使用這些。 使用中繼憑證比使用「未鏈結憑證」安全，因此常會見到 CA 使用中繼憑證。 中繼憑證通常是從 CA 網站個別下載而得。 本文章中的步驟提供關於如何確保任何中繼憑證與已上傳至應用程式的憑證相合併的步驟。

<a name="bkmk_certreq"></a>
### 使用 Certreq.exe 取得憑證 (僅限 Windows)

Certreq.exe 是一項用來建立憑證要求的 Windows 公用程式。 自 Windows XP/Windows Server 2000 起，它就已是基本 Windows 安裝的一部分，因此新版 Windows 系統上應該會提供。 請透過下列步驟，使用 certreq.exe 來取得 SSL 憑證。

1. 開啟 **記事本** 建立包含下列新文件。 取代 **mysite.com** 上與您的應用程式的自訂網域名稱的主旨。 例如，Subject = "CN=www.contoso.com"。

        [NewRequest]
        Subject = "CN=mysite.com"
        Exportable = TRUE
        KeyLength = 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1

    如需有關上面所指定的選項，以及其他可用選項的詳細資訊，請參閱 [Certreq 參考文件](http://technet.microsoft.com/library/cc725793.aspx)。

2. 儲存為文字檔 **myrequest.txt**。

3. 從 **[開始] 畫面** 或 **[開始] 功能表**, ，請執行 **cmd.exe**。

4. 在命令提示字元中，使用下列命令來建立憑證要求檔案：

        certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

    指定的路徑 **myrequest.txt** 在步驟 1，並建立時要使用的路徑建立檔案 **myrequest.csr** 檔案。

5. 提交 **myrequest.csr** 至憑證授權單位取得 SSL 憑證。 這可能包括上傳檔案，或在 [記事本] 中開啟檔案並將內容直接貼到 Web 表單。

    如需憑證授權單位的清單，請參閱 [Windows 和 Windows Phone 8 SSL 根憑證計劃 (成員 Ca)][cas] Microsoft TechNet Wiki 上。

6. 等到憑證授權單位提供憑證檔 (.CER) 給您後，將該檔案儲存至用來產生要求的電腦，然後使用下列命令來接受要求並完成憑證產生程序。

        certreq -accept -user mycert.cer

    在此情況下， **mycert.cer** 從憑證授權單位收到的憑證將用來完成憑證簽章。 沒有檔案會建立；憑證會儲存至 Windows 憑證存放區中。

6. 如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。 這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。 請選取針對 Microsoft IIS 所提供的版本。

    一旦您已下載的憑證，請以滑鼠右鍵按一下它在檔案總管]，然後選取 **安裝憑證**。 使用中的預設值 **憑證匯入精靈**, ，並持續選取 **下一步** 匯入完成為止。

7. 若要從憑證存放區匯出憑證，請執行 **certmgr.msc** 從 **[開始] 畫面** 或 **[開始] 功能表**。 當 **憑證管理員** 出現時，依序展開 **個人** 資料夾，然後再選取 **憑證**。 在 **發給** 欄位中，尋找您所要求的憑證的自訂網域名稱的項目。 在 **核發者** 欄位，它應該會列出您用於此憑證之憑證授權單位。

    ![在這裡插入憑證管理員的影像][certmgr]

9. 以滑鼠右鍵按一下憑證，然後選取 **所有工作**, ，然後選取 **匯出**。 在 **憑證匯出精靈**, ，按一下 [ **下一步** ，然後選取 **是，匯出私密金鑰**。 按一下 [ **下一步**。

    ![匯出私密金鑰][certwiz1]

10. 選取 **個人資訊交換-PKCS #12**, ，**包含憑證鏈結中的所有憑證**, ，和 **匯出所有延伸的內容**。 按一下 [ **下一步**。

    ![包括所有憑證和延伸內容][certwiz2]

11. 選取 **密碼**, ，然後輸入並確認密碼。 按一下 [ **下一步**。

    ![指定密碼][certwiz3]

12. 提供將包含所匯出憑證的路徑和檔名。 檔名的副檔名應該 **.pfx**。 按一下 [ **下一步** 完成程序。

    ![提供檔案路徑][certwiz4]

您現在可以將匯出的 PFX 檔案上傳至 Azure App Service 中的應用程式。

<a name="bkmk_openssl"></a>
### 使用 OpenSSL 取得憑證

1. 從命令列、Bash 或終端機工作階段中，使用下列命令產生私密金鑰和憑證簽署要求：

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. 當系統提示時，輸入適當資訊。 例如：

        Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []:

    此程序完成之後，您應該有兩個檔案: **myserver.key** 和 **server.csr**。  **Server.csr** 包含憑證簽署要求。

3. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。 如需憑證授權單位的清單，請參閱 [Windows 和 Windows Phone 8 SSL 根憑證計劃 (成員 Ca)][cas] Microsoft TechNet Wiki 上。

4. 從 CA 取得憑證之後, 將它儲存到名為 **myserver.crt**。 如果您的 CA 提供文字格式的憑證，只需要將憑證文字貼入 **myserver.crt** 檔案。 在文字編輯器中檢視檔案時，檔案內容應該與下列類似：

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

    儲存檔案。

5. 從命令列、 Bash 或終端機工作階段，請使用下列的命令，將 **myserver.key** 和 **myserver.crt** 到 **myserver.pfx**, ，Azure App Service 所需的格式:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    當系統提示時，輸入 .pfx 檔案的保護密碼。

    > [AZURE.NOTE] 如果 CA 使用中繼憑證，您必須先匯出憑證的下一個步驟來安裝這些憑證。 這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。 請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。
    >
    > 下列命令示範如何建立.pfx 檔案，其中包含中繼憑證，其包含在 **intermediate-cets.pem** 檔案:  
    >
    >
    `````
    openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
    `````

    執行此命令之後，您應該有 **myserver.pfx** 檔案適用於 Azure 應用程式服務搭配使用。

<a name="bkmk_iismgr"></a>
### 使用 IIS 管理員取得憑證

如果您熟悉 IIS 管理員，可以用它來產生可用於 Azure App Service 的憑證。

1. 使用 IIS 管理員產生 CSR，以傳送至憑證授權單位。 如需產生 CSR 的詳細資訊，請參閱 [要求網際網路伺服器憑證 (IIS 7)][iiscsr]。

2. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。 如需憑證授權單位的清單，請參閱 [Windows 和 Windows Phone 8 SSL 根憑證計劃 (成員 Ca)][cas] Microsoft TechNet Wiki 上。

3. 使用憑證授權單位廠商所提供的憑證來完成 CSR。 如需有關完成 CSR 的詳細資訊，請參閱 [安裝網際網路伺服器憑證 (IIS 7)][installcertiis]。

4. 如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。 這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。 請選取針對 Microsoft IIS 所提供的版本。

    一旦您已下載的憑證，請以滑鼠右鍵按一下它在檔案總管]，然後選取 **安裝憑證**。 使用中的預設值 **憑證匯入精靈**, ，並持續選取 **下一步** 匯入完成為止。

4. 匯出憑證，從 IIS 管理員如需匯出憑證的詳細資訊，請參閱 [匯出伺服器憑證 (IIS 7)][exportcertiis]。 後面的步驟會將匯出的檔案上傳至 Azure，以搭配應用程式使用。

    > [AZURE.NOTE] 在匯出過程中，請務必選取 [ <strong>是，匯出私密金鑰</strong>。 這樣會在匯出的憑證中納入私密金鑰。

    > [AZURE.NOTE] 在匯出過程中，請務必選取 [ **憑證路徑中包含的所有憑證** 和 **匯出所有延伸的內容**。 這樣會在匯出的憑證中納入所有中繼憑證。

<a name="bkmk_subjectaltname"></a>
### 使用 OpenSSL 取得 SubjectAltName 憑證

OpenSSL 可以用來建立憑證要求 (並讓該要求使用 SubjectAltName 延伸來透過單一憑證支援多個網域名稱)，不過，這需要有組態檔。 下列步驟會逐步引導您建立組態檔，然後使用它來要求憑證。

1. 建立新的檔名為 __sancert.cnf__ ，並使用下列做為檔案的內容:

        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----

    請注意開頭為 'subjectAltName' 的那行。 請將目前列出的網域名稱取代為您除了一般名稱外，還想要支援的網域名稱。 例如：

        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

    您不需要變更 commonName_default 欄位，因為系統會在下列其中一個步驟中提示您輸入一般名稱。

2. 儲存 __sancert.cnf__ 檔案。

1. 使用 sancert.cnf 組態檔，以產生私密金鑰和憑證簽署要求。 從 Bash 或終端機工作階段中，使用下列命令：

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. 當系統提示時，輸入適當資訊。 例如：

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com


    此程序完成之後，您應該有兩個檔案: **myserver.key** 和 **server.csr**。  **Server.csr** 包含憑證簽署要求。

3. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。 如需憑證授權單位的清單，請參閱 [Windows 和 Windows Phone 8 SSL 根憑證計劃 (成員 Ca)][cas] Microsoft TechNet Wiki 上。

4. 從 CA 取得憑證之後, 將它儲存到名為 **myserver.crt**。 如果您的 CA 提供文字格式的憑證，只需要將憑證文字貼入 **myserver.crt** 檔案。 在文字編輯器中檢視檔案時，檔案內容應該與下列類似：

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

    儲存檔案。

5. 從命令列、 Bash 或終端機工作階段，請使用下列的命令，將 **myserver.key** 和 **myserver.crt** 到 **myserver.pfx**, ，Azure App Service 所需的格式:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    當系統提示時，輸入 .pfx 檔案的保護密碼。

    > [AZURE.NOTE] 如果 CA 使用中繼憑證，您必須先匯出憑證的下一個步驟來安裝這些憑證。 這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。 請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。  
    >
    > 下列命令示範如何建立.pfx 檔案，其中包含中繼憑證，其包含在 **intermediate-cets.pem** 檔案:  
    >
    >
    `````
    openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
    `````

    執行此命令之後，您應該有 **myserver.pfx** 檔案適用於 Azure 應用程式服務搭配使用。

<a name="bkmk_selfsigned"></a>
### 產生自我簽署的憑證 (僅供測試)

在某些情況下，您可能會想要取得憑證來進行測試，並等到要實際投入使用時，才將向信任 CA 購買憑證。 自我簽署憑證就有這樣的功能。 自我簽署憑證是您將自己當成憑證授權單位來建立和簽署的憑證。 雖然此憑證可以用來保護應用程式，但是大部分的瀏覽器在造訪應用程式時將會傳回錯誤，因為憑證不是由信任的 CA 所簽署。 部分瀏覽器甚至會拒絕讓您檢視應用程式。

- [使用 makecert 產生自我簽署憑證](#bkmk_ssmakecert)
- [使用 OpenSSL 產生自我簽署憑證](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### 使用 makecert 產生自我簽署憑證 ####

您可以在已安裝 Visual Studio 的 Windows 系統上執行下列步驟來建立測試憑證：

1. 從 **開始功能表** 或 **[開始] 畫面**, ，搜尋 **開發人員命令提示字元**。 最後，以滑鼠右鍵按一下 **開發人員命令提示字元** ，然後選取 **系統管理員身分執行**。

    如果您收到 [使用者帳戶控制] 對話方塊，選取 **是** 以繼續。

2. 從 [開發人員命令提示字元] 中，使用下列命令建立新的自我簽署憑證。 您必須將 **serverdnsname** 與您的應用程式的 DNS。

        makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

    此命令會建立有效日期為 01/01/2013 到 01/01/2014 的憑證，而且會將位置儲存在 CurrentUser 憑證存放區中。

3. 從 **開始功能表** 或 **[開始] 畫面**, ，搜尋 **Windows PowerShell** 和啟動此應用程式。

4. 從 Windows PowerShell 提示字元中，使用下列命令匯出先前建立的憑證：

        $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
        get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

    這為安全字串 $mypwd 中，將指定的密碼，然後使用所指定的 DNS 名稱來尋找憑證 **dnsname** 參數，然後匯出至所指定的檔案 **filepath** 參數。 而含有密碼的安全字串會用來保護匯出的檔案。

<a name="bkmk_ssopenssl"></a>
####使用 OpenSSL 產生自我簽署憑證 ####

1. 建立名為的新文件 **serverauth.cnf**, ，做為此檔案的內容，使用下列命令:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your app's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

    這會指定產生 Azure App Service 可用之 SSL 憑證所需的組態設定。

2. 從命令列、Bash 或終端機工作階段中，使用下列命令產生新的自我簽署憑證：

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    這會建立新的憑證，使用指定的組態設定 **serverauth.cnf** 檔案。

3. 若要將憑證匯出為可上傳至 Azure App Service 中應用程式的 .PFX 檔案，請使用下列命令：

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    當系統提示時，輸入 .pfx 檔案的保護密碼。

     **Myserver.pfx** 所產生的這個命令可以用來保護您的應用程式進行測試。

<a name="bkmk_standardmode"></a>
## 2.設定標準定價層

為自訂網域僅適用於啟用 HTTPS **標準** Azure App Service 中的層。 使用下列步驟切換至您的應用程式服務計劃 **標準** 層。

> [AZURE.NOTE] 從應用程式切換之前 **免費** 層到 **標準** 層，您應該移除訂閱的支出上限，而變得無法使用您的應用程式如果達到在計費期間結束前的支出。 如需有關共用和 **標準** 層，請參閱 [定價詳細資料][pricing]。

1.  在瀏覽器中開啟 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。
2.  按一下 [ **瀏覽** 左邊頁面的選項。
3.  按一下 [ **Web 應用程式** 刀鋒視窗。
4.  按一下應用程式的名稱。
5.  在 **Essentials** 頁面上，按一下 **設定**。
6.  按一下 [ **小數位數**
    ![調整索引標籤][scale]
7.  在 **延展** 區段中，按一下 [設定應用程式服務計劃模式 **選取**。
    ![定價層][] sslreserved

    > [AZURE.NOTE] 如果您收到 「 設定 web 應用程式的規模 ' & lt; 應用程式名稱 & gt;' 失敗 」 錯誤，可以使用 [詳細資料] 按鈕以取得詳細資訊。 您可能會收到 「 沒有足夠的可用標準執行個體伺服器可滿足此要求 」。 發生錯誤。 如果您收到此錯誤，請連絡 [Azure 支援](/support/options/)。

<a name="bkmk_configuressl"></a>
## 3.在應用程式中設定 SSL

在執行本節中的步驟之前，您必須先建立自訂網域名稱與應用程式的關聯。 如需詳細資訊，請參閱 [設定 web 應用程式的自訂網域名稱][customdomain]。

1.  在瀏覽器中開啟 [Azure 管理入口網站](https://portal.azure.com)。
2.  按一下 [ **瀏覽** 左邊頁面的選項。
3.  按一下 [ **Web 應用程式** 刀鋒視窗。
4.  按一下應用程式的名稱。
5.  在 **Essentials** 頁面上，按一下 **設定**。
6.  按一下 [ **自訂網域和 SSL**。
    ![The config] tab[] sslconfig
7.  在 **憑證** 區段中，按一下 **上傳**
8.  使用 **上傳憑證** 對話方塊中，選取稍早使用 [IIS 管理員建立的.pfx 憑證檔案或 OpenSSL。 指定當初用來保護 .pfx 檔案的密碼 (如果有的話)。 最後，按一下 [ **儲存** 以上傳憑證。
    ![ssl] upload[] ssluploadcert
9. 在 **ssl 繫結** 區段 **SSL 設定** 索引標籤上，使用下拉式清單選取要使用 SSL，以及要使用的憑證保護的網域名稱。 您也可以選取是否要使用 [伺服器名稱指示][sni] (SNI) 還是 IP SSL。

    ![SSL 繫結][sslbindings]

    * IP SSL 會將伺服器的專用公用 IP 位址對應至網域名稱，以建立憑證與網域名稱的關聯。 這需要與您服務相關聯的每個網域名稱 (contoso.com、fabricam.com 等) 都有專用 IP 位址。 這是傳統用來建立 SSL 憑證與網頁伺服器之關聯的方法。

    * Sni SSL 是 SSL 的延伸模組和 [傳輸層安全性][tls] (TLS)，可讓多個網域共用相同的 IP 位址，而有不同的安全性憑證，每個網域。 現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI，不過，較舊的瀏覽器可能不支援 SNI。 如需 SNI 的詳細資訊，請參閱 [伺服器名稱指示][sni] Wikipedia 上的文件。

10. 按一下 [ **儲存** ，儲存變更並啟用 SSL。

> [AZURE.NOTE] 如果您選取 **IP 為主的 SSL** 和 A 記錄設定您的自訂網域，您必須執行下列額外步驟:
>
> 1. 設定 IP SSL 繫結之後，您的應用程式將會獲派專用的 IP 位址。 您可以找到這個 IP 位址上 **儀表板** 應用程式頁面，請在 **快速瀏覽** 一節。 它會列出成為 **虛擬 IP 位址**:
>    
>     ![虛擬 IP 位址](./media/configure-ssl-web-site/staticip.png)
>    
>     請注意，此 IP 位址與先前用來設定您網域之 A 記錄的虛擬 IP 位址不同。 如果您設定成使用 SNI SSL，或未設定成使用 SSL，則不會列出此項目的位址。
>
> 2. 使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟的 IP 位址。


此時，您應該能夠瀏覽您的應用程式使用 `HTTPS://` 而不是 `HTTP://` 確認憑證已正確設定。

<a name="bkmk_enforce"></a>
## 4.在應用程式上強制使用 HTTPS

Azure App Service 「 *不* 強制使用 HTTPS。 訪客可能仍會使用 HTTP 存取您的應用程式，而這可能會危及應用程式的安全性。 如果您想要強制您的應用程式使用 HTTPS，您可以使用 **URL 重寫** 模組。 Azure App Service 隨附 URL Rewrite 模組，此模組可讓您定義連入要求在送達應用程式之前要套用的規則。 **它可以用於 Azure 所支援的任何程式設計語言撰寫的應用程式。**

> [AZURE.NOTE] .NET MVC 應用程式應使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選，而非 URL Rewrite。 如需使用 RequireHttps 的詳細資訊，請參閱 [安全的 ASP.NET MVC 5 應用程式部署至 web 應用程式](../articles/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。
>
> 如需使用其他程式設計語言和架構的程式設計方式重新導向要求的相關資訊，請參閱這些技術的文件。

URL 重寫規則定義在 **web.config** 檔案儲存在應用程式的根目錄中。 下列範例包含可強制所有連入流量使用 HTTPS 的基本 URL Rewrite 規則。

<a name="example"></a>**URL Rewrite 範例 Web.Config**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

此規則的運作方式是當使用者使用 HTTP 要求頁面時，便傳回 HTTP 狀態碼 301 (永久重新導向)。 301 會將要求重新導向至與訪客要求相同的 URL，但使用 HTTPS 來取代要求的 HTTP 部分。 例如，HTTP://contoso.com 會被重新導向至 HTTPS://contoso.com。

> [AZURE.NOTE] 如果您的應用程式以撰寫  **Node.js**, ，**PHP**, ，**Python Django**, ，或 **Java**, ，它可能不包含 web.config 檔案。 不過 **Node.js**, ，**Python Django**, ，和 **Java** 都會確實用到裝載於 Azure App Service 的 web.config-Azure 檔案會自動建立在部署期間，因此您永遠不會看到它。 如果您在應用程式中加入一個 web.config 檔案，則它將覆寫 Azure 自動產生的同名檔案。

###.NET

對於.NET 應用程式，修改您的應用程式的 web.config 檔案，並新增 **& lt; 重寫 >** > 一節 [範例](#example) 至 **(& s) lt; 系統。Web 伺服器 >** 一節。

如果您的 web.config 檔案已經包含 **(& s) lt; 重寫 >** 區段中，新增 **(& s) lt; 規則 >** 從 [範例](#example) 中的第一個項目 **& lt; 規則 >** 區段。

###PHP

若是 PHP 應用程式，只要儲存 [範例](#example) 為您的應用程式的根目錄中的 web.config 檔案，然後重新部署您的應用程式的應用程式。

###Node.js、Python Django 和 Java

如果 Node.js、Python Django 和 Java 應用程式未提供 web.config 檔案，則此檔案會自動建立，但因為此檔案會在部署過程中建立，因此它只會存在於伺服器上。 這個自動產生的檔案會包含告知 Azure 如何主控應用程式的設定。

若要從應用程式擷取並修改自動產生的檔案，請使用下列步驟。

1. 使用 FTP 下載檔案 (請參閱 [透過 FTP 和收集診斷記錄檔上傳/下載檔案](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx))。

2. 將檔案新增至應用程式的根目錄。

3. 使用下列資訊新增重寫規則。

    * **Node.js 和 Python Django**

        為 Node.js 和 Python Django 應用程式便已產生的 web.config 檔案 **& lt; 重寫 >** 區段，其中包含 **(& s) lt; 規則 >** 項目所需的應用程式正常運作。 若要強制應用程式以使用 HTTPS，請新增 **(& s) lt; 規則 >** 範例中的第一個項目中 **& lt; 規則 >** 區段。 這會在保留其他規則不變的情況下強制使用 HTTPS。

    * **Java**

        使用 Apache Tomcat 的 Java 應用程式的 web.config 檔案不包含 **& lt; 重寫 >** 區段，因此您必須新增 **(& s) lt; 重寫 >** 區段到範例 **(& s) lt;system.webServer >** 區段。

4. 將專案 (包含更新的 web.config) 重新部署至 Azure

一旦部署包含重寫規則以強制使用 HTTPS 的 web.config 後，它應會立即生效並將所有要求重新導向至 HTTPS。

如需有關 IIS URL Rewrite 模組的詳細資訊，請參閱 [URL 重寫](http://www.iis.net/downloads/microsoft/url-rewrite) 文件。

## 其他資源 ##
- [Microsoft Azure 信任中心](/support/trust-center/security/)
- [Azure 網站中解除鎖定的組態選項](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [啟用診斷記錄](../articles/app-service-web/web-sites-enable-diagnostic-log.md)
- [在 Azure App Service 中設定 Web 應用程式](../articles/app-service-web/web-sites-configure.md)
- [Azure 管理入口網站](https://manage.windowsazure.com)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需舊入口網站變更為新入口網站的指南，請參閱: [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

