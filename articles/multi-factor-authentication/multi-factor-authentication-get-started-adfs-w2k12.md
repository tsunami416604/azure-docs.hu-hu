---
title: "MFA-kiszolgáló és AD FS a Windows Serveren | Microsoft Docs"
description: "Ez a cikk az Azure Multi-Factor Authentication és az AD FS Windows Server 2012 R2 és 2016 alatti használatának első lépéseit mutatja be."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 57208068-1e55-45b6-840f-fdcd13723074
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: kgremban
ms.reviewer: 
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 7fc6ad052e4e873be6a3e7009e9739e4a1c9ce03
ms.contentlocale: hu-hu
ms.lasthandoff: 09/20/2017

---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Azure Multi-Factor Authentication-kiszolgáló konfigurálása az AD FS-sel való használathoz Windows Serveren
Ha Active Directory összevonási szolgáltatásokat (AD FS-t) használ, és szeretne védelmet biztosítani a felhőnek vagy a helyszíni erőforrásoknak, konfigurálhat egy Azure Multi-Factor Authentication-kiszolgálót, és beállíthatja, hogy az együttműködjön az AD FS-sel. Ezzel aktiválja a kétlépéses ellenőrzést a nagy értékű végpontokon.

Ebben a cikkben azt mutatjuk be, hogyan használja az Azure Multi-Factor Authentication-kiszolgálót az AD FS-sel Windows Server 2012 R2 vagy Windows Server 2016 rendszeren. További információkért olvassa el a [cikket, amely leírja, hogy hogyan biztosítson védelmet a felhőnek és helyszíni erőforrásainak az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0 segítségével](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>A Windows Server AD FS védelme az Azure Multi-Factor Authentication-kiszolgálóval
Az Azure Multi-Factor Authentication-kiszolgáló telepítéséhez a következő lehetőségek állnak rendelkezésre:

* Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS-sel azonos kiszolgálóra
* Az Azure Multi-Factor Authentication-adapter helyi telepítése az AD FS-kiszolgálóra, majd a Multi-Factor Authentication-kiszolgáló telepítése egy másik számítógépre

Mielőtt megkezdi a műveletet, vegye figyelembe az alábbi információkat:

* Nem szükséges telepítenie az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra. Az AD FS Multi-Factor Authentication-adapterét azonban olyan Windows Server 2012 R2 vagy Windows Server 2016 rendszerű gépre kell telepítenie, amelyen az AD FS fut. A kiszolgálót másik számítógépre is telepítheti, ha az AD FS-adaptert külön telepíti az AD FS összevonási kiszolgálóra. Az adapter külön történő telepítésével kapcsolatos információkért olvassa el az alábbi eljárást.
* Ha szervezete SMS-es vagy mobilalkalmazásos ellenőrzési módszereket használ, a vállalati beállításokban meghatározott karakterláncok tartalmazzák az <$*application_name*$> helyőrzőt. Az MFA-kiszolgáló 7.1-es verziójában megadhat egy alkalmazásnevet, amely felváltja a helyőrzőt. A 7.0-ás vagy régebbi verziókban a rendszer nem cseréli le automatikusan a helyőrzőt az AD FS-adapter használatakor. Ezekben a régebbi verziókban távolítsa el a helyőrzőt a megfelelő karakterláncokból az AD FS védelmének kialakításakor.
* A bejelentkezéshez használt fióknak felhasználói jogosultsággal kell rendelkeznie a biztonsági csoportok létrehozásához az Active Directory szolgáltatásban.
* A Multi-Factor Authentication AD FS-adapter telepítővarázslója létrehoz egy PhoneFactor-adminisztrátorok nevű biztonsági csoportot az Active Directory-példányban, majd hozzáadja az összevonási szolgáltatáshoz tartozó AD FS-szolgáltatásfiókot a csoporthoz. Ellenőrizze, hogy létrejött-e a PhoneFactor-adminisztrátorok csoport a tartományvezérlőn, illetve hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.
* További információ a Web Service SDK felhasználói portállal végzett telepítéséről: [A felhasználói portál üzembe helyezése Azure Multi-Factor Authentication-kiszolgálón](multi-factor-authentication-get-started-portal.md).

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS kiszolgálóján
1. Töltse le és telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra. A telepítéssel kapcsolatos információkért olvassa el [az Azure Multi-Factor Authentication-kiszolgáló használatának első lépéseit bemutató cikket](multi-factor-authentication-get-started-server.md).
2. Az Azure Multi-Factor Authentication-kiszolgáló felügyeleti konzolján kattintson az **AD FS** ikonra. Válassza a **Felhasználó beléptetésének engedélyezése** és **Módszer kiválasztásának engedélyezése a felhasználóknak** beállításokat.
3. Kapcsolja be a további funkciókat, amelyekre vállalatának szüksége van.
4. Kattintson az **AD FS-adapter telepítése** gombra.
   
   <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>

5. Ha az Active Directory ablak jelenik meg, ez két dolgot jelent. A számítógép tartományhoz csatlakozik, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító Active Directory-konfiguráció hiányos. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **Az Active Directory automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet. Kattintson a **Tovább** gombra.
6. Ha a Helyi csoport ablak jelenik meg, ez két dolgot jelent. A számítógép nem csatlakozik tartományhoz, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító helyi csoport konfigurációja hiányos. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be **A helyi csoport automatikus konfigurálásának kihagyása és a beállítások manuális megadása** jelölőnégyzetet. Kattintson a **Tovább** gombra.
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
2. Állítsa a **WebServiceSdkUrl** értékét a Multi-Factor Authentication-webszolgáltatás SDK URL-címére. Például:  *https://contoso.com/&lt;tanúsítványnév&gt;/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx*. A *tanúsítványnév* a tanúsítvány nevét jelöli.  
3. Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 szkriptet: adja hozzá a `-ConfigurationFilePath &lt;path&gt;` értéket a `Register-AdfsAuthenticationProvider` parancs végéhez. A *&lt;path&gt;* helyére írja be a MultiFactorAuthenticationAdfsAdapter.config fájl teljes elérési útját.

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
6. Az IIS-kezelőben kattintson duplán a **Konfigurációszerkesztő** lehetőségre a Web Service SDK virtuális könyvtárat tartalmazó webhelyen. Fontos, hogy a webhelyet válassza ki, ne a virtuális könyvtárat.  
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

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Az Azure AD-erőforrások AD FS-sel való védelme
A felhőszolgáltatás biztosításához állítson be egy jogcímszabályt, hogy az Active Directory összevonási szolgáltatások a multipleauthn jogcímet adja ki, amikor egy felhasználó sikeresen végez kétlépéses ellenőrzést. Ez a jogcím átkerül az Azure AD-re. Az alábbi eljárás bemutatja ennek lépéseit:

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **Függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése…** lehetőséget.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

4. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** elemre.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

6. Adjon nevet a szabálynak.
7. Válassza a **Hitelesítési módszerek hivatkozásai** lehetőséget a Bejövő jogcím típusaként.
8. Válassza **Az összes jogcímérték továbbítása** lehetőséget.
    ![Átalakítási jogcímszabály hozzáadása varázsló](./media/multi-factor-authentication-get-started-adfs-cloud/configurewizard.png)
9. Kattintson a **Befejezés** gombra. Zárja be az AD FS felügyeleti konzolt.

## <a name="related-topics"></a>Kapcsolódó témakörök
Ha segítségre van szüksége a hibaelhárításhoz, tekintse meg az [Azure Multi-Factor Authenticationnel kapcsolatos gyakori kérdéseket](multi-factor-authentication-faq.md).

