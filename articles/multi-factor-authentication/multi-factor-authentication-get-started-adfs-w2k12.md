<properties
    pageTitle="A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával | Microsoft Azure"
    description="Ez a cikk az Azure Multi-Factor Authentication és az AD FS Windows Server 2012 R2 alatti használatának első lépéseit mutatja be."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS segítségével Windows Server 2012 R2 rendszerben

Ha szervezete Active Directory összevonási szolgáltatásokat (AD FS-t) használ, és szeretne védelmet biztosítani a felhőnek vagy a helyszíni erőforrásoknak, helyezzen üzembe és konfiguráljon egy Azure Multi-Factor Authentication-kiszolgálót, és állítsa be, hogy az együttműködjön az AD FS-sel. Ezzel többtényezős hitelesítést aktiválhat a nagy értékű végpontokon.

Ebben a cikkben azt mutatjuk be, hogyan használja az Azure Multi-Factor Authentication-kiszolgálót az AD FS-sel Windows Server 2012 R2 rendszeren. További információkért olvassa el a [cikket, amely leírja, hogy hogyan biztosítson védelmet a felhőnek és helyszíni erőforrásainak az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0 segítségével](multi-factor-authentication-get-started-adfs-adfs2.md).

## A Windows Server 2012 R2 AD FS védelme az Azure Multi-Factor Authentication-kiszolgálóval

Az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez a következő lehetőségek állnak rendelkezésre:

- Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS-sel azonos kiszolgálóra
- Az Azure Multi-Factor Authentication-adapter helyi telepítése az AD FS-kiszolgálóra, majd a Multi-Factor Authentication-kiszolgáló telepítése egy másik számítógépre

Mielőtt megkezdi a műveletet, vegye figyelembe az alábbi információkat:

- Az Azure Multi-Factor Authentication-kiszolgálót nem szükséges feltétlenül az AD FS-kiszolgálóra telepítenie. Az AD FS Multi-Factor Authentication-adapterét azonban olyan Windows Server 2012 R2 rendszerű gépre kell telepítenie, amelyen az AD FS fut. A kiszolgálót másik számítógépre is telepítheti, ha az támogatott verziójú. Ekkor az AD FS-adaptert külön telepítheti az AD FS összevonási kiszolgálóra. Az adapter külön történő telepítésével kapcsolatos információkért olvassa el az alábbi eljárást.
- A Multi-Factor Authentication-kiszolgáló AD FS-adapterének tervezésekor az volt az elvárás, hogy az AD FS át tudja adni a függő entitás nevét az adapternek, amely az alkalmazás neveként szolgálhat. Azonban kiderült, hogy nem így van. Ha szervezete SMS-es vagy mobilalkalmazásos hitelesítési módszereket használ, a vállalati beállításokban meghatározott karakterláncok tartalmazzák az <$*application_name*$> helyőrzőt. A rendszer nem cseréli le automatikusan a helyőrzőt, amikor megkezdi az AD FS-adapter használatát. Javasoljuk, hogy az AD FS védelmének kialakításakor távolítsa el a helyőrzőt a megfelelő karakterláncokból.

- A bejelentkezéshez használt fióknak felhasználói jogosultsággal kell rendelkeznie a biztonsági csoportok létrehozásához az Active Directory szolgáltatásban.

- A Multi-Factor Authentication AD FS-adapter telepítővarázslója létrehoz egy PhoneFactor-adminisztrátorok nevű biztonsági csoportot az Active Directory-példányban, majd hozzáadja az összevonási szolgáltatáshoz tartozó AD FS-szolgáltatásfiókot a csoporthoz. Javasoljuk, hogy ellenőrizze a tartományvezérlőn, hogy valóban létrejött-e a PhoneFactor-adminisztrátorok csoport, illetve, hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.
- A Web Service SDK-nak a felhasználói portállal végzett telepítésével kapcsolatos információkért olvassa el [a felhasználói portál üzembe helyezését az Azure Multi-Factor Authentication-kiszolgálón bemutató cikket](multi-factor-authentication-get-started-portal.md).


### Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS kiszolgálóján

1. Töltse le és telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS összevonási kiszolgálóra. A telepítéssel kapcsolatos információkért olvassa el [az Azure Multi-Factor Authentication-kiszolgáló használatának első lépéseit bemutató cikket](multi-factor-authentication-get-started-server.md).
2. Az Azure Multi-Factor Authentication-kiszolgáló felügyeleti konzolján kattintson az **AD FS** ikonra, majd jelölje be a következő jelölőnégyzeteket: **Felhasználó beléptetésének engedélyezése** és **Módszer kiválasztásának engedélyezése a felhasználóknak**.
3. Kapcsolja be a további funkciókat, amelyekre vállalatának szüksége van.
4. Kattintson az **AD FS-adapter telepítése** gombra.
<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Ha a számítógép tartományhoz csatlakozik, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító Active Directory-konfiguráció hiányos, az **Active Directory** ablak jelenik meg. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **Az Active Directory automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet, és kattintson a **Tovább** gombra.
6. Ha a számítógép nem csatlakozik tartományhoz, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító helyicsoport-konfiguráció hiányos, a **Helyi csoport** ablak jelenik meg. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **A helyi csoport automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet, és kattintson a **Tovább** gombra.
7. A telepítővarázslóban kattintson a **Tovább** gombra. Az Azure Multi-Factor Authentication-kiszolgáló létrehozza a PhoneFactor-adminisztrátorok csoportot, és hozzáadja az AD FS-szolgáltatásfiókot a PhoneFactor-adminisztrátorok csoporthoz.
<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. A **Telepítő indítása** lapon kattintson a **Tovább** gombra.
9. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a **Tovább** gombra.
10. Amikor a telepítés befejeződött, kattintson a **Bezárás** gombra.
11. Ha befejeződött az adapter telepítése, regisztrálnia kell azt az AD FS-sel. Indítsa el a Windows PowerShellt, és futtassa az alábbi parancsot:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Módosítsa a globális hitelesítési szabályzatot az AD FS-ben úgy, hogy az engedélyezze az újonnan regisztrált adapter használatát. Az AD FS felügyeleti konzoljában nyissa meg a **Hitelesítési házirendek** csomópontot. A **Többtényezős hitelesítés** résznél kattintson a **Globális beállítások** mellett található **Szerkesztés** hivatkozásra. A **Globális hitelesítési házirend szerkesztése** ablakban válassza a **Többtényezős hitelesítés** lehetőséget további hitelesítési módszerként, majd kattintson az **OK** gombra. Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven. A regisztráció csak az AD FS szolgáltatás újraindítása után lép életbe.

<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Ezen a ponton elértük, hogy a Multi-Factor Authentication-kiszolgáló úgy van beállítva, hogy további hitelesítésszolgáltatóként működjön az AD FS szolgáltatásokhoz.

## Az AD FS-adapter önálló példányának telepítése a Web Service SDK segítségével
1. Telepítse a Web Service SDK-t a Multi-Factor Authentication-kiszolgálót futtató kiszolgálón.
2. Másolja a következő fájlokat a \Program Files\Multi-Factor Authentication Server könyvtárból a kiszolgálóra, amelyen telepíteni fogja az AD FS-adaptert:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Futtassa a MultiFactorAuthenticationAdfsAdapterSetup64.msi telepítőfájlt.
4. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a **Tovább** gombra a telepítés elvégzéséhez.
5. Amikor a telepítés befejeződött, kattintson a **Bezárás** gombra.
6. Szerkessze a MultiFactorAuthenticationAdfsAdapter.config fájlt a következőképpen:

|MultiFactorAuthenticationAdfsAdapter.config lépés| Allépés|
|:------------- | :------------- |
|Állítsa a **UseWebServiceSdk** csomópontot **true** értékre.||
|Állítsa a **WebServiceSdkUrl** értékét a Multi-Factor Authentication-webszolgáltatás SDK URL-címére.</br></br>Példa:  **https://contoso.com/&lt;tanúsítványnév&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**</br></br>A tanúsítványnév helyére írja be saját tanúsítványa nevét. ||
|Végezze el a Web Service SDK konfigurálását.<br><br>*1. lehetőség*: felhasználónévvel és jelszóval.|<ol type="a"><li>Állítsa a **WebServiceSdkUsername** értékét olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja. Használja a &lt;tartomány&gt;&#92;&lt;felhasználónév&gt; formátumot.<li>Állítsa a **WebServiceSdkPassword** értékét a megfelelő fiókjelszóra.</li></ol>
|Végezze el a Web Service SDK *további* konfigurálását.<br><br>*2. lehetőség*: ügyféltanúsítvánnyal.|<ol type="a"><li>Szerezzen be ügyféltanúsítványt a Web Service SDK-t futtató kiszolgáló hitelesítésszolgáltatójától. Ismerje meg, hogyan [szerezheti be az ügyféltanúsítványt](https://technet.microsoft.com/library/cc770328.aspx).</li><li>Importálja az ügyféltanúsítványt a helyi számítógép személyes tanúsítványtárolójába a Web Service SDK-t futtató kiszolgálón. Megjegyzés: Győződjön meg róla, hogy a hitelesítésszolgáltató nyilvános tanúsítványa szerepel-e a megbízható legfelső szintű tanúsítványok tanúsítványtárolójában.</li><li>Exportálja az ügyféltanúsítvány nyilvános és titkos kulcsát egy .pfx fájlba.</li><li>Exportálja a nyilvános kulcsot Base64 formátumban egy .cer-fájlba.</li><li>A Kiszolgálókezelőben ellenőrizze, hogy a Webkiszolgáló (IIS)\Webkiszolgáló\Biztonság\IIS ügyféltanúsítvány-hozzárendelés hitelesítése szolgáltatás telepítve van-e. Ha nincs telepítve, válassza a **Szerepkörök és szolgáltatások hozzáadása** lehetőséget a szolgáltatás hozzáadásához.</li><li>Az IIS-kezelőben kattintson duplán a **Konfigurációszerkesztő** lehetőségre a Web Service SDK virtuális könyvtárat tartalmazó webhelyen. Megjegyzés: Nagyon fontos, hogy ezt a webhely szintjén végezze, ne a virtuális könyvtár szintjén.</li><li>Lépjen a **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** szakaszra.</li><li>Az **enabled** elemet állítsa **true** értékre.</li><li>Állítsa a **oneToOneCertificateMappingsEnabled** elemet **true** értékre.</li><li>Kattintson a **oneToOneMappings** melletti **...** gombra, majd kattintson a **Hozzáadás** hivatkozásra.</li><li>Nyissa meg a korábban exportált Base64 .cer-fájlt. Távolítsa el a *-----BEGIN CERTIFICATE-----* és az *-----END CERTIFICATE-----* elemet, továbbá minden sortörést. Másolja az eredményül kapott karakterláncot.</li><li>Állítsa a **certificate** értékét az előző lépésben másolt karakterláncra.</li><li>Az **enabled** elemet állítsa **true** értékre.</li><li>Állítsa a **userName** értékét egy olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja. Használja a &lt;tartomány&gt;&#92;&lt;felhasználónév&gt; formátumot.</li><li>Állítsa be a jelszót és a megfelelő fiókjelszót, majd zárja be a Konfigurációszerkesztőt.</li><li>Kattintson az **Alkalmaz** hivatkozásra.</li><li>A Web Service SDK virtuális könyvtárában kattintson duplán a **Hitelesítés** lehetőségre.</li><li>Győződjön meg arról, hogy az **ASP.NET megszemélyesítés** és az **Alapszintű hitelesítés** **Engedélyezve** értékre, és a többi elem pedig **Letiltva** értékre van-e állítva.</li><li>A Web Service SDK virtuális könyvtárában kattintson duplán az **SSL-beállítások** lehetőségre.</li><li>Adja meg az **Ügyféltanúsítványok** számára az **Elfogadás** értéket, és kattintson az **Alkalmaz** gombra.</li><li>Másolja a korábban exportált .pfx-fájlt az AD FS-adaptert futtató kiszolgálóra.</li><li>Importálja a .pfx-fájlt a helyi számítógép személyes tanúsítványtárolójába.</li><li>Kattintson jobb gombbal, majd válassza a **Titkos kulcsok kezelése** lehetőséget, és adjon olvasási hozzáférést az AD FS szolgáltatásba való bejelentkezéshez használt fióknak.</li><li>Nyissa meg az ügyféltanúsítványt, és másolja az ujjlenyomatot a **Részletek** lapról.</li><li>A MultiFactorAuthenticationAdfsAdapter.config fájlban állítsa a **WebServiceSdkCertificateThumbprint** értékét az előző lépésben másolt karakterláncra.</li></ol>
| Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájlt: adja hozzá a *-ConfigurationFilePath &lt;path&gt;* értéket a `Register-AdfsAuthenticationProvider` parancs végéhez. A *&lt;path&gt;* helyére írja be a MultiFactorAuthenticationAdfsAdapter.config fájl teljes elérési útját.||

Az adapter regisztrálásához futtassa a PowerShellben a \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájlt. Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven. A regisztráció csak az AD FS szolgáltatás újraindítása után lép életbe.



<!--HONumber=Sep16_HO3-->


