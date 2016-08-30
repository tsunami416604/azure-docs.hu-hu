<properties 
    pageTitle="A felhő és a helyszíni erőforrások védelme az Azure MFA-kiszolgáló és a Windows Server 2012 R2 AD FS használatával" 
    description="Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát a Windows Server 2012 R2-n." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>


# A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával

Ha a szervezete Azure AD-összevonást használ, és olyan helyszíni vagy felhőbeli erőforrásokkal rendelkezik, amelyeket meg szeretne védeni, konfigurálja az Azure Multi-Factor Authentication-kiszolgálót az AD FS-sel való együttműködésre, hogy a többtényezős hitelesítés elinduljon a magas értékű végpontokhoz.

Ez a dokumentáció az Azure Multi-Factor Authentication-kiszolgáló AD FS szolgáltatásokkal való használatáról szól a Windows Server 2012 R2-n.  Az Azure Multi-Factor Authentication az AD FS 2.0-s verziójával való használatával kapcsolatos információért lásd: [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával](multi-factor-authentication-get-started-adfs-adfs2.md).

## A Windows Server 2012 R2 AD FS védelme az Azure Multi-Factor Authentication-kiszolgálóval

Az Azure Multi-Factor Authentication-kiszolgáló telepítésekor a következő két lehetősége van:

- Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS kiszolgálóján 
- Az Azure Multi-Factor Authentication adapter helyi telepítése az AD FS kiszolgálón és az MFA-kiszolgáló telepítése másik számítógépen

Mielőtt megkezdi a műveletet, vegye figyelembe az alábbi információkat:

- Nem követelmény, hogy az Azure Multi-Factor Authentication-kiszolgálót az AD FS összevonási kiszolgálón telepítse, de az AD FS Multi-Factor Authentication adapterét AD FS-t futtató Windows Server 2012 R2-n kell telepíteni. A kiszolgálót másik számítógépen is telepítheti, ha az támogatott verziójú. Ekkor az AD FS-adaptert külön telepítheti az AD FS összevonási kiszolgálón. Az adapter önálló telepítésének utasításait az alábbi eljárás tartalmazza.
- A Multi-Factor Authentication-kiszolgáló AD FS-adapterének tervezésekor az volt az elvárás, hogy az AD FS át tudja adni a függő entitás nevét az adapternek, amely az alkalmazás neveként szolgálhat.  Azonban kiderült, hogy nem így van.  Ha szöveges üzenetet vagy mobilalkalmazásos hitelesítési módszereket használ, a vállalati beállításokban meghatározott karakterláncok egy <$application_name$> helyőrzőt tartalmaznak.  Ez a helyőrző nem cserélődik le az AD FS-adapter használatakor.  Ezért ajánlott eltávolítani a helyőrzőt a megfelelő karakterláncok közül az AD FS védelmének biztosítása során.

- A bejelentkezett fióknak jogosultságokkal kell rendelkeznie biztonsági csoportok létrehozására az Active Directoryban.

- A Multi-Factor Authentication AD FS-adapter telepítővarázslója létrehoz egy PhoneFactor-adminisztrátorok nevű biztonsági csoportot az Active Directoryban, majd hozzáadja ehhez a csoporthoz az összevonási szolgáltatás AD FS-szolgáltatásfiókját. Ajánljuk, hogy ellenőrizze a tartományvezérlőn, hogy valóban létrejött-e a PhoneFactor-adminisztrátorok csoport, és hogy az AD FS-szolgáltatásfiók tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.
- A Web Service SDK felhasználói portállal végzett telepítésével kapcsolatos információért lásd: [A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóhoz](multi-factor-authentication-get-started-portal.md).
  

### Az Azure Multi-Factor Authentication-kiszolgáló helyi telepítése az AD FS kiszolgálóján

1. Töltse le és telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS összevonási kiszolgálóra. Az Azure Multi-Factor Authentication-kiszolgáló telepítésével kapcsolatos további információért lásd: [Azure Multi-Factor Authentication-kiszolgáló – első lépések](multi-factor-authentication-get-started-server.md)
2. Az Azure Multi-Factor Authentication-kiszolgáló felhasználói felületén válassza ki az AD FS ikont, és jelölje be a Felhasználó beléptetésének engedélyezése és a Módszer kiválasztásának engedélyezése a felhasználóknak beállítást.
3. Válassza ki a további kívánt beállításokat.
4. Kattintson az AD FS-adapter telepítése gombra.
<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Ha a számítógép tartományhoz csatlakozik, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító Active Directory-konfiguráció hiányos, az Active Directory lépés jelenik meg.  Kattintson a Tovább gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be Az Active Directory automatikus konfigurálásának kihagyása és a beállítások manuális megadása jelölőnégyzetet, és kattintson a Tovább gombra.
6. Ha a számítógép nem csatlakozik tartományhoz, és az AD FS-adapter és a Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító helyicsoport-konfiguráció hiányos, a helyi csoport lépése jelenik meg.  Kattintson a Tovább gombra a konfiguráció automatikus elvégzéséhez, vagy jelölje be A helyi csoport automatikus konfigurálásának kihagyása és a beállítások manuális megadása jelölőnégyzetet, és kattintson a Tovább gombra.
7. Ez megjeleníti a telepítővarázslót. Itt kattintson a Tovább gombra, hogy az Azure Multi-Factor Authentication-kiszolgáló létrehozhassa a PhoneFactor-adminisztrátorok csoportot és az AD FS-szolgáltatásfiókot a PhoneFactor-adminisztrátorok csoporthoz adja.
<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. A Telepítő indítása lépésben kattintson a Tovább gombra.
9. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a Tovább gombra.
10. Amikor a telepítés befejeződött, kattintson a Bezárás gombra.
11. Most, hogy az adapter telepítve van, regisztrálni kell az AD FS-sel. Nyissa meg a Windows PowerShellt, és futtassa a következőt: C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Most szerkesztenünk kell a globális hitelesítési házirendet az AD FS-ben, hogy az újonnan regisztrált adaptert használja. Az AD FS felügyeleti konzolon navigáljon a Hitelesítési házirendek csomóponthoz, és a Multi-factor Authentication szakaszban kattintson a Globális beállítások alszakasz melletti Szerkesztés hivatkozásra. A Globális hitelesítési házirend szerkesztése ablakban válassza a Többtényezős hitelesítés lehetőséget további hitelesítési módszerként, majd kattintson az OK gombra. Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven.  A regisztráció csak az AD FS szolgáltatás újraindítása után lép életbe.

<center>![Felhő](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Ekkor a Multi-Factor Authentication-kiszolgáló úgy van beállítva, hogy további hitelesítésszolgáltató legyen az AD FS szolgáltatásokhoz.

## Az önálló AD FS-adapter telepítése a Web Service SDK-val
1. Telepítse a Web Service SDK-t a Multi-Factor Authentication-kiszolgálót futtató kiszolgálón.
2. Másolja a MultiFactorAuthenticationAdfsAdapterSetup64.msi, a Register-MultiFactorAuthenticationAdfsAdapter.ps1, a Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 és a MultiFactorAuthenticationAdfsAdapter.config fájlt a \Program Files\Multi-Factor Authentication Server könyvtárból arra a kiszolgálóra, amelyre az AD FS-adaptert telepíteni szeretné.
3. Futtassa a MultiFactorAuthenticationAdfsAdapterSetup64.msi fájlt.
4. A Multi-Factor Authentication AD FS-adapter telepítőjében kattintson a Tovább gombra a telepítés elvégzéséhez.
5. Amikor a telepítés befejeződött, kattintson a Bezárás gombra.
6. Szerkessze a MultiFactorAuthenticationAdfsAdapter.config fájlt a következőképpen:

MultiFactorAuthenticationAdfsAdapter.config lépés| Allépés
:------------- | :------------- |
Állítsa a UseWebServiceSdk csomópontot true értékre.||
Állítsa a WebServiceSdkUrl értékét a Multi-Factor Authentication webszolgáltatás SDK URL-címére.||
1. lehetőség – A Web Service SDK konfigurálása felhasználónévvel és jelszóval.|<ol><li>Állítsa a WebServiceSdkUsername értékét olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja.  Használja a <domain>\<felhasználónév> formátumot.<li>Állítsa a WebServiceSdkPassword értékét a megfelelő fiókjelszóra.</li></ol>
2. lehetőség – A Web Service SDK konfigurálása ügyféltanúsítvánnyal.|<ol><li>Szerezzen be ügyféltanúsítványt a Web Service SDK-t futtató kiszolgáló hitelesítésszolgáltatójától.  A tanúsítvány beszerzésével kapcsolatos információért lásd: [Obtain Client Certificate](https://technet.microsoft.com/library/cc770328(v=ws.10).aspx) (Ügyféltanúsítvány beszerzése).</li><li>Importálja az ügyféltanúsítványt a helyi számítógép személyes tanúsítványtárolójába a Web Service SDK-t futtató kiszolgálón.  Megjegyzés: Győződjön meg arról, hogy a hitelesítésszolgáltató nyilvános tanúsítványa szerepel a megbízható főtanúsítványok között.</li><li>Exportálja az ügyféltanúsítvány nyilvános és titkos kulcsát egy .pfx fájlba.</li><li>Exportálja a nyilvános kulcsot base-64 formátumban egy .cer fájlba.</li><li>A Kiszolgálókezelőben ellenőrizze, hogy a Webkiszolgáló (IIS)\Webkiszolgáló\Biztonság\IIS ügyféltanúsítvány-hozzárendelés hitelesítése szolgáltatás telepítve van-e.</li><li>Ha nincs telepítve, válassza a Szerepkörök és szolgáltatások hozzáadása lehetőséget a szolgáltatás hozzáadásához.</li><li>Az IIS-kezelőben kattintson duplán a Konfigurációszerkesztőre a Web Service SDK virtuális könyvtárat tartalmazó webhelyen.  Megjegyzés: Nagyon fontos, hogy ezt a webhely szintjén végezze, ne a virtuális könyvtár szintjén.</li><li>Lépjen a system.webServer/security/authentication/iisClientCertificateMappingAuthentication szakaszra.</li><li>Az enabled elemet állítsa true értékűre.</li><li>Állítsa a oneToOneCertificateMappingsEnabled elemet true értékűre.</li><li>Kattintson a oneToOneMappings melletti ... gombra.</li><li>Kattintson a Hozzáadás hivatkozásra.</li><li>Nyissa meg a korábban exportált base-64 .cer fájlt.  Távolítsa el a -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- elemet és minden sortörést.  Másolja az eredményül kapott karakterláncot.</li><li>Állítsa a tanúsítványt az előző lépésben másolt karakterláncra.</li><li>Az enabled elemet állítsa true értékűre.</li><li>Állítsa a userName nevet olyan fiókra, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja.  Használja a <domain>\<felhasználónév> formátumot.</li><li>Állítsa a jelszót a megfelelő fiókjelszóra.</li><li>Zárja be a Gyűjteményszerkesztőt.</li><li>Kattintson az Alkalmaz hivatkozásra.</li><li>Ugorjon a Web Service SDK virtuális könyvtárra.</li><li>Kattintson duplán a Hitelesítés elemre.</li><li>Győződjön meg arról, hogy az ASP.NET megszemélyesítés és az alapszintű hitelesítés engedélyezve van, és a többi elem le van tiltva.</li><li>Ugorjon ismét a Web Service SDK virtuális könyvtárra.</li><li>Kattintson duplán az SSL-beállításokra.</li><li>Adja meg az Ügyféltanúsítványok számára az Elfogadás értéket, és kattintson az Alkalmaz gombra.</li><li>Másolja a korábban exportált .pfx fájlt az AD FS-adaptert futtató kiszolgálóra.</li><li>Importálja a .pfx fájlt a helyi számítógép személyes tanúsítványtárolójába.</li><li>Válassza a helyi menü Titkos kulcsok kezelése parancsát, és biztosítson olvasási hozzáférést azon fiók számára, amelyként az Active Directory összevonási szolgáltatások be van jelentkezve.</li><li>Nyissa meg az ügyféltanúsítványt, és másolja az ujjlenyomatot a Részletek lapról.</li><li>A MultiFactorAuthenticationAdfsAdapter.config fájlban állítsa a WebServiceSdkCertificateThumbprint értékét az előző lépésben másolt karakterláncra.</li></ol>
Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 szkriptet a -ConfigurationFilePath <path> a Register-AdfsAuthenticationProvider parancs végéhez adásával, ahol a <path> a MultiFactorAuthenticationAdfsAdapter.config fájl teljes elérési útja.|


Most futtassa a \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 szkriptet a PowerShellben az adapter regisztrálásához.  Lezajlik az adapter regisztrálása WindowsAzureMultiFactorAuthentication néven.  A regisztráció csak az AD FS szolgáltatás újraindítása után lép életbe. 




























 

 


 

 


 





 


 

























































































 


 

 






 


<!--HONumber=jun16_HO2-->


