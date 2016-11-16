---
title: "MFA-kiszolgáló és a Windows Server 2012 R2 AD FS | Microsoft Docs"
description: "Ez a cikk az Azure Multi-Factor Authentication és az AD FS Windows Server 2012 R2 alatti használatának első lépéseit mutatja be."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 57208068-1e55-45b6-840f-fdcd13723074
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 86a4bc7ea89416f2c67626439f08fa615a2e6511


---
# <a name="secure-your-cloud-and-onpremises-resources-using-azure-multifactor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS használatával a Windows Server 2012 R2 rendszeren
Ha Active Directory összevonási szolgáltatásokat (AD FS-t) használ, és szeretne védelmet biztosítani a felhőnek vagy a helyszíni erőforrásoknak, konfigurálhat egy Azure Multi-Factor Authentication-kiszolgálót, és beállíthatja, hogy az együttműködjön az AD FS-sel. Ezzel aktiválja a kétlépéses ellenőrzést a nagy értékű végpontokon.

Ebben a cikkben azt mutatjuk be, hogyan használja az Azure Multi-Factor Authentication-kiszolgálót az AD FS-sel Windows Server 2012 R2 rendszeren. További információkért olvassa el a [cikket, amely leírja, hogy hogyan biztosítson védelmet a felhőnek és helyszíni erőforrásainak az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0 segítségével](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multifactor-authentication-server"></a>A Windows Server 2012 R2 AD FS védelme az Azure Multi-Factor Authentication-kiszolgálóval
Az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez a következő lehetőségek állnak rendelkezésre:

* Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS-sel azonos kiszolgálóra
* Az Azure Multi-Factor Authentication-adapter helyi telepítése az AD FS-kiszolgálóra, majd a Multi-Factor Authentication-kiszolgáló telepítése egy másik számítógépre

Mielőtt megkezdi a műveletet, vegye figyelembe az alábbi információkat:

* Az Azure Multi-Factor Authentication-kiszolgálót nem szükséges feltétlenül az AD FS-kiszolgálóra telepítenie. Az AD FS Multi-Factor Authentication-adapterét azonban olyan Windows Server 2012 R2 rendszerű gépre kell telepítenie, amelyen az AD FS fut. A kiszolgálót másik számítógépre is telepítheti, ha az támogatott verziójú. Ekkor az AD FS-adaptert külön telepítheti az AD FS összevonási kiszolgálóra. Az adapter külön történő telepítésével kapcsolatos információkért olvassa el az alábbi eljárást.
* Az MFA-kiszolgáló AD FS-adapterének tervezésekor az volt az elvárás, hogy az AD FS át tudja adni a függő entitás nevét az adapternek. Ezt követően a függő entitás neve használható lett volna az alkalmazás neveként. Azonban kiderült, hogy nem így van. Ha szervezete SMS-es vagy mobilalkalmazásos ellenőrzési módszereket használ, a vállalati beállításokban meghatározott karakterláncok tartalmazzák az <$*application_name*$> helyőrzőt. A rendszer nem cseréli le automatikusan a helyőrzőt, amikor megkezdi az AD FS-adapter használatát. Javasoljuk, hogy az AD FS védelmének kialakításakor távolítsa el a helyőrzőt a megfelelő karakterláncokból.
* A bejelentkezéshez használt fióknak felhasználói jogosultsággal kell rendelkeznie a biztonsági csoportok létrehozásához az Active Directory szolgáltatásban.
* A Multi-Factor Authentication AD FS-adapter telepítővarázslója létrehoz egy PhoneFactor-adminisztrátorok nevű biztonsági csoportot az Active Directory-példányban, majd hozzáadja az összevonási szolgáltatáshoz tartozó AD FS-szolgáltatásfiókot a csoporthoz. Javasoljuk, hogy ellenőrizze a tartományvezérlőn, hogy valóban létrejött-e a PhoneFactor-adminisztrátorok csoport, illetve, hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.
* A Web Service SDK-nak a felhasználói portállal végzett telepítésével kapcsolatos információkért olvassa el [a felhasználói portál üzembe helyezését az Azure Multi-Factor Authentication-kiszolgálón bemutató cikket](multi-factor-authentication-get-started-portal.md).

### <a name="install-azure-multifactor-authentication-server-locally-on-the-ad-fs-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS kiszolgálóján
1. Töltse le és telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra. A telepítéssel kapcsolatos információkért olvassa el [az Azure Multi-Factor Authentication-kiszolgáló használatának első lépéseit bemutató cikket](multi-factor-authentication-get-started-server.md).
2. Az Azure Multi-Factor Authentication-kiszolgáló felügyeleti konzolján kattintson az **AD FS** ikonra, majd jelölje be a következő jelölőnégyzeteket: **Felhasználó beléptetésének engedélyezése** és **Módszer kiválasztásának engedélyezése a felhasználóknak**.
3. Kapcsolja be a további funkciókat, amelyekre vállalatának szüksége van.
4. Kattintson az **AD FS-adapter telepítése** gombra.
   <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Ha az Active Directory ablak jelenik meg, ez két dolgot jelent. A számítógép tartományhoz csatlakozik, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító Active Directory-konfiguráció hiányos. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **Az Active Directory automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet, és kattintson a **Tovább** gombra.
6. Ha a Helyi csoport ablak jelenik meg, ez két dolgot jelent. A számítógép nem csatlakozik tartományhoz, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító helyi csoport konfigurációja hiányos. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **A helyi csoport automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet, és kattintson a **Tovább** gombra.
7. A telepítővarázslóban kattintson a **Tovább** gombra. Az Azure Multi-Factor Authentication-kiszolgáló létrehozza a PhoneFactor-adminisztrátorok csoportot, és hozzáadja az AD FS-szolgáltatásfiókot a PhoneFactor-adminisztrátorok csoporthoz.
   <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. A **Telepítő indítása** lapon kattintson a **Tovább** gombra.
9. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a **Tovább** gombra.
10. Amikor a telepítés befejeződött, kattintson a **Bezárás** gombra.
11. Ha befejeződött az adapter telepítése, regisztrálnia kell azt az AD FS-sel. Indítsa el a Windows PowerShellt, és futtassa az alábbi parancsot:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
    <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Az újonnan regisztrált adapter használatához módosítsa a globális hitelesítési szabályzatot az AD FS-ben. Az AD FS felügyeleti konzoljában nyissa meg a **Hitelesítési házirendek** csomópontot. A **Többtényezős hitelesítés** résznél kattintson a **Globális beállítások** mellett található **Szerkesztés** hivatkozásra. A **Globális hitelesítési házirend szerkesztése** ablakban válassza a **Többtényezős hitelesítés** lehetőséget további hitelesítési módszerként, majd kattintson az **OK** gombra. Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven. A regisztráció érvénybe léptetéséhez indítsa újra az AD FS szolgáltatást.

<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Ezen a ponton elértük, hogy a Multi-Factor Authentication-kiszolgáló úgy van beállítva, hogy további hitelesítésszolgáltatóként működjön az AD FS szolgáltatásokhoz.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Az AD FS-adapter önálló példányának telepítése a Web Service SDK segítségével
1. Telepítse a Web Service SDK-t a Multi-Factor Authentication-kiszolgálót futtató kiszolgálón.
2. Másolja a következő fájlokat a \Program Files\Multi-Factor Authentication Server könyvtárból a kiszolgálóra, amelyen telepíteni fogja az AD FS-adaptert:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Futtassa a MultiFactorAuthenticationAdfsAdapterSetup64.msi telepítőfájlt.
4. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a **Tovább** gombra a telepítés megkezdéséhez.
5. Amikor a telepítés befejeződött, kattintson a **Bezárás** gombra.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Szerkessze a MultiFactorAuthenticationAdfsAdapter.config fájlt
A MultiFactorAuthenticationAdfsAdapter.config fájl szerkesztéséhez kövesse az alábbi lépéseket:

1. Állítsa a **UseWebServiceSdk** csomópontot **true** értékre.  
2. Állítsa a **WebServiceSdkUrl** értékét a Multi-Factor Authentication-webszolgáltatás SDK URL-címére. Például: *https://contoso.com/&lt;tanúsítványnév&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx* A tanúsítványnév a tanúsítvány nevét jelöli.  
3. Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájlt: adja hozzá a *-ConfigurationFilePath &lt;path&gt;* értéket a `Register-AdfsAuthenticationProvider` parancs végéhez. A *&lt;path&gt;* helyére írja be a MultiFactorAuthenticationAdfsAdapter.config fájl teljes elérési útját.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>A Web Service SDK konfigurálása felhasználónévvel és jelszóval
Két lehetőség érhető el a Web Service SDK konfigurálására. A felhasználónévvel és jelszóval, illetve az ügyféltanúsítvánnyal történő konfigurálás. Kövesse az alábbi lépéseket, ha az első lehetőséget választja, vagy ugorjon előre, ha a másodikat.  

1. Állítsa a **WebServiceSdkUsername** értékét olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja. Használja a &lt;tartomány&gt;&#92;&lt;felhasználónév&gt; formátumot.  
2. Állítsa a **WebServiceSdkPassword** értékét a megfelelő fiókjelszóra.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>A Web Service SDK konfigurálása ügyféltanúsítvánnyal
Ha nem szeretne felhasználónevet és jelszót használni, az alábbi lépések követésével végezze el a Web Service SDK konfigurálását ügyféltanúsítvánnyal.

1. Szerezzen be ügyféltanúsítványt a Web Service SDK-t futtató kiszolgáló hitelesítésszolgáltatójától. Ismerje meg, hogyan [szerezheti be az ügyféltanúsítványt](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importálja az ügyféltanúsítványt a helyi számítógép személyes tanúsítványtárolójába a Web Service SDK-t futtató kiszolgálón. Győződjön meg róla, hogy a hitelesítésszolgáltató nyilvános tanúsítványa szerepel-e a megbízható legfelső szintű tanúsítványok tanúsítványtárolójában.  
3. Exportálja az ügyféltanúsítvány nyilvános és titkos kulcsát egy .pfx fájlba.  
4. Exportálja a nyilvános kulcsot Base64 formátumban egy .cer-fájlba.  
5. A Kiszolgálókezelőben ellenőrizze, hogy a Webkiszolgáló (IIS)\Webkiszolgáló\Biztonság\IIS ügyféltanúsítvány-hozzárendelés hitelesítése szolgáltatás telepítve van-e. Ha nincs telepítve, válassza a **Szerepkörök és szolgáltatások hozzáadása** lehetőséget a szolgáltatás hozzáadásához.  
6. Az IIS-kezelőben kattintson duplán a **Konfigurációszerkesztő** lehetőségre a Web Service SDK virtuális könyvtárat tartalmazó webhelyen. Nagyon fontos, hogy ezt a webhely szintjén végezze, ne a virtuális könyvtár szintjén.  
7. Lépjen a **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** szakaszra.  
8. Az enabled elemet állítsa **true** értékre.  
9. Állítsa a oneToOneCertificateMappingsEnabled elemet **true** értékre.  
10. Kattintson a oneToOneMappings melletti **...** gombra, majd kattintson a **Hozzáadás** hivatkozásra.  
11. Nyissa meg a korábban exportált Base64 .cer-fájlt. Távolítsa el a *-----BEGIN CERTIFICATE-----* és az *-----END CERTIFICATE-----* elemet, továbbá minden sortörést. Másolja az eredményül kapott karakterláncot.  
12. Állítsa a tanúsítvány értékét az előző lépésben másolt karakterláncra.  
13. Az enabled elemet állítsa **true** értékre.  
14. Állítsa a userName nevet olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja. Használja a &lt;tartomány&gt;&#92;&lt;felhasználónév&gt; formátumot.  
15. Állítsa be a jelszót és a megfelelő fiókjelszót, majd zárja be a Konfigurációszerkesztőt.  
16. Kattintson az **Alkalmaz** hivatkozásra.  
17. A Web Service SDK virtuális könyvtárában kattintson duplán a **Hitelesítés** lehetőségre.  
18. Győződjön meg arról, hogy az ASP.NET megszemélyesítés és az Alapszintű hitelesítés **Engedélyezve**, a többi elem pedig **Letiltva** értékre van állítva.  
19. A Web Service SDK virtuális könyvtárában kattintson duplán az **SSL-beállítások** lehetőségre.  
20. Adja meg az ügyféltanúsítványok számára az **Elfogadás** értéket, és kattintson az **Alkalmaz** gombra.  
21. Másolja a korábban exportált .pfx-fájlt az AD FS-adaptert futtató kiszolgálóra.  
22. Importálja a .pfx-fájlt a helyi számítógép személyes tanúsítványtárolójába.  
23. Kattintson jobb gombbal, majd válassza a **Titkos kulcsok kezelése** lehetőséget, és adjon olvasási hozzáférést az AD FS szolgáltatásba való bejelentkezéshez használt fióknak.  
24. Nyissa meg az ügyféltanúsítványt, és másolja az ujjlenyomatot a **Részletek** lapról.  
25. A MultiFactorAuthenticationAdfsAdapter.config fájlban állítsa a **WebServiceSdkCertificateThumbprint** értékét az előző lépésben másolt karakterláncra.  

Végezetül futtassa a \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 szkriptet a PowerShellben az adapter regisztrálásához. Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven. A regisztráció érvénybe léptetéséhez indítsa újra az AD FS szolgáltatást.

## <a name="related-topics"></a>Kapcsolódó témakörök
Ha segítségre van szüksége a hibaelhárításhoz, tekintse meg az [Azure Multi-Factor Authenticationnel kapcsolatos gyakori kérdéseket](multi-factor-authentication-faq.md).




<!--HONumber=Nov16_HO2-->


