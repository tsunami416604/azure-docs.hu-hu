<properties
    pageTitle="Az Azure AD Connect Health-ügynök telepítése | Microsoft Azure"
    description="Ez az Azure AD Connect Health lap, amely bemutatja az AD FS- és a Sync-ügynök telepítését."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/05/2016"
    ms.author="vakarand"/>


# Az Azure AD Connect Health-ügynök telepítése

Ez a dokumentum végigvezeti az Azure AD Connect Health ügynökök telepítésének és konfigurálásának folyamatán. Az ügynököt [innen](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent) töltheti le.

##  Követelmények
Az alábbi táblázat az Azure AD Connect Health használatának követelményeit sorolja fel.

| Követelmény | Leírás|
| ----------- | ---------- |
|Azure AD Premium| Az Azure AD Connect Health egy Azure AD Premium szolgáltatás, amelyhez Azure AD Premium szükséges. </br></br>További információkért lásd: [Ismerkedés az Azure AD Premium szolgáltatással](active-directory-get-started-premium.md) </br>Egy 30 napos ingyenes próbaverzió indításához lásd: [Próbaverzió indítása.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Az Azure AD Connect Health szolgáltatás indításához az Azure AD szolgáltatásban globális rendszergazdának kell lennie|Alapértelmezés szerint kizárólag a globális rendszergazdák telepíthetik és konfigurálhatják az állapotügynököket, hogy azok elinduljanak, a portálhoz hozzáférjenek, és műveletek hajtsanak végre az Azure AD Connect Health szolgáltatásban. További információkért lásd: [Az Azure AD-címtár felügyelete](active-directory-administer.md). <br><br> A szerepköralapú hozzáférés-vezérlés használatával hozzáférést engedhet az Azure AD Connect Health szolgáltatáshoz más felhasználók számára is a szervezetben. További információért lásd: [Role Based Access Control for Azure AD Connect Health](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) (Szerepköralapú hozzáférés-vezérlés az Azure AD Connect Health szolgáltatáshoz). </br></br>**Fontos:** Az ügynökök telepítésekor használt fióknak munkahelyi vagy iskolai fióknak kell lennie, és nem lehet Microsoft-fiók. További információkért lásd: [Regisztráció az Azure-ba szervezetként](sign-up-organization.md)
|Az Azure AD Connect Health-ügynököt az összes célkiszolgálóra telepíteni kell| Az Azure AD Connect Health használatához szükséges, hogy mindegyik célkiszolgálón telepítve legyen egy ügynök, amely a portálon megtekintett adatokat szolgáltatja. </br></br>Ha például az AD FS helyszíni infrastruktúrájával kapcsolatos adatokat kíván gyűjteni, az ügynököt telepíteni kell az AD FS-kiszolgálókra, az AD FS proxykiszolgálókra és a webalkalmazás-proxy kiszolgálókra. Szintén telepíteni kell az ügynököt a tartományvezérlőkre, ha a helyszíni AD DS-infrastruktúrával kapcsolatos adatokat kíván gyűjteni. </br></br>**Fontos:** Az ügynökök telepítésekor használt fióknak munkahelyi vagy iskolai fióknak kell lennie, és nem lehet Microsoft-fiók.   További információkért lásd: [Regisztráció az Azure-ba szervezetként](sign-up-organization.md)|
|Kimenő kapcsolódás az Azure szolgáltatásvégpontokra|A telepítés és a futásidő során az ügynöknek kapcsolódnia kell az Azure AD Connect Health alább felsorolt szolgáltatásvégpontjaira. Amennyiben blokkolta a kimenő kapcsolatokat, bizonyosodjon meg róla, hogy az alábbiak fel vannak véve az engedélyezett célok listájára: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net – port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Az ügynököt futtató kiszolgáló tűzfalportjai.| Az ügynök a következőt tűzfalportok megnyitását igényli, hogy kommunikálhasson az Azure AD Health szolgáltatásvégpontjaival.</br></br><li>TCP/UDP port: 443</li><li>TCP/UDP port: 5671</li>
|Az alábbi webhelyek engedélyezése, amennyiben az Internet Explorer - Fokozott biztonsági beállítások be van kapcsolva|Az alábbi webhelyeket engedélyezni kell, amennyiben az Internet Explorer - Fokozott biztonsági beállítások be van kapcsolva a kiszolgálón, amelyen az ügynök telepítve lesz.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>A szervezet Azure Active Directory által megbízhatóként megjelölt összevonási kiszolgálója. Például: https://sts.contoso.com</li>




## Az Azure AD Connect Health-ügynök telepítése az AD FS szolgáltatáshoz
Az ügynök telepítésének indításához kattintson duplán a letöltött .exe-fájlra. Az első képernyőn kattintson az Install (Telepítés) elemre.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install1.png)

Amint a telepítés befejeződött, kattintson a Configure Now (Konfigurálás most) gombra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install2.png)

Ekkor megnyílik egy parancssor, majd egy PowerShell parancsmag, amely végrehajtja a Register-AzureADConnectHealthADFSAgent parancsot. A rendszer kéri majd, hogy jelentkezzen be az Azure-ba. Lépjen tovább, és jelentkezzen be.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install3.png)


A bejelentkezést követően a PowerShell továbblép. Amint befejeződött, bezárhatja a PowerShellt, és a konfiguráció véget ér.

Ezen a ponton a szolgáltatásoknak automatikusan el kell indulniuk, és az ügynök innentől fogva figyeli és gyűjti az adatokat.  Vegye figyelembe, hogy figyelmeztetéseket fog látni a PowerShell-ablakban, amennyiben nem teljesítette az előző szakaszokban vázolt összes előfeltételt. Mindenképp végezze el ezeket a követelményeket [itt](active-directory-aadconnect-health-agent-install.md#requirements), mielőtt folytatná az ügynök telepítését. Az alábbi képernyő egy példa az ilyen hibákra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install4.png)

Az ügynök telepítésének ellenőrzéséhez nyissa meg a szolgáltatásokat, és keresse a következőket. A szolgáltatásoknak futniuk kell, ha a konfigurációt elvégezte. Ha nem végezte el, akkor nem indulnak el, amíg a konfigurálást be nem fejezte.

- Azure AD Connect Health AD FS Diagnostics szolgáltatás
- Azure AD Connect Health AD FS Insights szolgáltatás
- Azure AD Connect Health AD FS Monitoring szolgáltatás

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install5.png)


### Ügynökök telepítése Windows Server 2008 R2 kiszolgálókon

Windows Server 2008 R2 kiszolgálók esetén tegye a következőket:

1. Győződjön meg arról, hogy a kiszolgálón az 1. vagy annál magasabb szervizcsomag fut.
1. Az ügynök telepítéséhez kapcsolja ki az Internet Explorer - Fokozott biztonsági beállításokat:
1. Telepítse a Windows PowerShell 4.0 szolgáltatást mindegyik kiszolgálón az AD Health-ügynök telepítését megelőzően.  A Windows PowerShell 4.0 telepítése:
 - Telepítse a [Microsoft .NET keretrendszer 4.5-ös verzióját](https://www.microsoft.com/download/details.aspx?id=40779) az alábbi hivatkozás segítségével, ahonnan letöltheti az offline telepítőt.
 - Telepítse a PowerShell ISE-t (a Windows-szolgáltatásokból)
 - Telepítse a [Windows Management Framework 4.0 keretrendszert.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Telepítse az Internet Explorer 10-es vagy újabb verzióját a kiszolgálón. Ez szükséges ahhoz, hogy a Health Service hitelesíteni tudja Önt Azure-rendszergazdai hitelesítő adataival.
1. A Windows PowerShell 4.0 a Windows Server 2008 R2 rendszeren való telepítésével kapcsolatos további információkért lásd a wikicikket [ezen a helyen](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### AD FS-naplózás engedélyezése

Annak érdekében, hogy a használatelemzés szolgáltatás adatokat gyűjthessen és elemezhessen, az Azure AD Connect Health-ügynöknek szüksége van az AD FS-naplókra. Ezek a naplók alapértelmezés szerint nincsenek bekapcsolva. Ez csak az AD FS összevonási kiszolgálókra vonatkozik. Az AD FS proxykiszolgálókon és a webalkalmazás-proxy kiszolgálókon nem szükséges engedélyeznie a naplózást. Az AD FS-naplózás engedélyezéséhez és az AD FS-naplók helyének meghatározásához kövesse az alábbi lépéseket.

#### Az AD FS 2.0 naplózásának engedélyezése

1. Kattintson a **Start** gombra, mutasson a **Programok**, majd a **Felügyeleti eszközök** pontra, végül kattintson a **Helyi biztonsági házirend** parancsra.
2. Lépjen a **Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kezelése** mappára, majd kattintson duplán a Biztonsági naplózás létrehozása elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS 2.0 szolgáltatásfiók szerepel-e a listában. Ha nincs a listában, a **Felhasználó vagy csoport hozzáadása** gombra kattintva adja hozzá, majd kattintson az **OK** gombra.
4. Nyisson meg egy parancssort emelt szintű jogosultságokkal, és futtassa a következő parancsot a naplózás engedélyezéséhez.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Zárja be a Helyi biztonsági házirendet, majd nyissa meg a felügyeleti beépülő modult.  A felügyeleti beépülő modul megnyitásához kattintson a **Start** gombra, mutasson a **Programok**, majd a **Felügyeleti eszközök** pontra, végül kattintson az AD FS 2.0 Management parancsra.
6. A Műveletek panelen kattintson az Összevonási szolgáltatás tulajdonságainak szerkesztése elemre.
7. Az **Összevonási szolgáltatás tulajdonságai** párbeszédpanelen kattintson az **Események** lapra.
8. Jelölje be a **Sikernaplók** és a **Hibanaplók** jelölőnégyzeteket.
9. Kattintson az **OK** gombra.

#### Az AD FS naplózásának engedélyezése Windows Server 2012 R2 rendszeren

1. A **Helyi biztonsági házirend** megnyitásához kattintson a **Kiszolgálókezelő** elemre a Kezdőképernyőn, vagy a Kiszolgálókezelő elemre az asztali tálcán, majd kattintson az **Eszközök/Helyi biztonsági házirend** elemre.
2. Lépjen a **Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kiosztása** mappára, majd kattintson duplán a **Biztonsági naplózás létrehozása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS szolgáltatásfiók szerepel-e a listában. Ha nincs a listában, a **Felhasználó vagy csoport hozzáadása** gombra kattintva adja hozzá, majd kattintson az **OK** gombra.
4. Nyisson meg egy parancssort emelt szintű jogosultságokkal, és futtassa a következő parancsot a naplózás engedélyezéséhez: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Zárja be a **Helyi biztonsági házirendet**, majd nyissa meg az **AD FS kezelő** beépülő modulját (a Kiszolgálókezelőben kattintson az Eszközök, majd az AD FS kezelő elemre).
6. A Műveletek panelen kattintson az **Összevonási szolgáltatás tulajdonságainak szerkesztése** elemre.
7. Az Összevonási szolgáltatás tulajdonságai párbeszédpanelen kattintson az **Események** lapra.
8. Jelölje be a **Sikernaplók és a Hibanaplók** jelölőnégyzeteket, majd kattintson az **OK** gombra.






#### Az AD FS-naplók helyének meghatározása


1. Nyissa meg az **Eseménynaplót**.
2. Lépjen a Windows-naplókra, és válassza a **Biztonság** elemet.
3. A jobb oldalon kattintson az **Aktuális naplók szűrése** lehetőségre.
4. Az Eseményforrás alatt válassza az **AD FS-naplózás** elemet.

![AD FS-naplók](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Amennyiben rendelkezik olyan csoportházirenddel, amely letiltja az AD FS-naplózást, az Azure AD Connect Health-ügynök nem tudja majd gyűjteni az adatokat. Győződjön meg róla, hogy nem rendelkezik olyan csoportházirenddel, amely letiltaná a naplózást.

[//]: # (Start of Agent Proxy Configuration Section)

## Az Azure AD Connect Health-ügynök telepítése szinkronizáláshoz
Az Azure AD Connect Health szinkronizálási ügynöke automatikusan települ az Azure AD Connect legújabb buildjével.  Az Azure AD Connect szinkronizáláshoz való használatához le kell töltenie és telepítenie kell annak legújabb verzióját.  A legújabb verziót [innen](http://www.microsoft.com/download/details.aspx?id=47594) töltheti le.

Az ügynök telepítésének ellenőrzéséhez nyissa meg a szolgáltatásokat, és keresse a következőket. A szolgáltatásoknak futniuk kell, ha a konfigurációt elvégezte. Ha nem végezte el, akkor nem indulnak el, amíg a konfigurálást be nem fejezte.

- Azure AD Connect Health Sync Insights szolgáltatás
- Azure AD Connect Health Sync Monitoring szolgáltatás

![Az Azure AD Connect Health for Sync ellenőrzése](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Ne feledje, hogy az Azure AD Connect Health használatához az Azure AD Premium megléte szükséges.  Ha nem rendelkezik az Azure AD Premium szolgáltatással, nem tudja elvégezni a konfigurálást az Azure portálon.  További információkért lásd a követelményeket [itt](active-directory-aadconnect-health-agent-install.md#requirements).


## Az Azure AD Connect Health for Sync manuális regisztrációja
Ha az Azure AD Connect sikeres telepítését követően az Azure AD Connect Health for Sync-ügynök regisztrációja meghiúsul, a következő PowerShell-parancs használatával manuálisan regisztrálhatja az ügynököt.

>[AZURE.IMPORTANT] A PowerShell-parancs használata csak akkor szükséges, ha az ügynök regisztrálása meghiúsul az Azure AD Connect telepítése után.

Az alábbi PowerShell-parancs CSAK akkor szükséges, ha az állapotügynök regisztrálása meghiúsul az Azure AD Connect sikeres telepítése és konfigurációja után. Ilyen esetekben az Azure AD Connect Health szolgáltatások NEM indulnak el, amíg az ügynök nem lett sikeresen telepítve.

A szinkronizálási Azure AD Connect Health-ügynök manuálisan a következő PowerShell-paranccsal telepíthető:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

A parancs a következő paramétereket fogadja:

- AttributeFiltering : $true (alapértelmezett) – ha az AD Connect nem szinkronizálja az alapértelmezett attribútumkészletet. és testre lett szabva, hogy egy szűrt attribútumkészletet használjon. $false a többi esetben.
- StagingMode : $false (alapértelmezett) – ha az Azure AD Connect-kiszolgáló NEM átmeneti módban van, és $true, ha a kiszolgáló átmeneti módra lett konfigurálva.

Amikor a rendszer a hitelesítő adatait kéri, használja ugyanazt a globális rendszergazdai fiókot (például admin@domain.onmicrosoft.com), amelyet az Azure AD Connect konfigurálásához használt.

## Az Azure AD Connect Health-ügynök telepítése az AD DS szolgáltatáshoz
Az ügynök telepítésének indításához kattintson duplán a letöltött .exe-fájlra. Az első képernyőn kattintson az Install (Telepítés) elemre.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Amint a telepítés befejeződött, kattintson a Configure Now (Konfigurálás most) gombra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Ekkor megnyílik egy parancssor, majd egy PowerShell parancsmag, amely végrehajtja a Register-AzureADConnectHealthADDSAgent parancsot. A rendszer kéri majd, hogy jelentkezzen be az Azure-ba. Lépjen tovább, és jelentkezzen be.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

A bejelentkezést követően a PowerShell továbblép. Amint befejeződött, bezárhatja a PowerShellt, és a konfiguráció véget ér.

Ezen a ponton a szolgáltatásoknak automatikusan el kell indulniuk, és az ügynök innentől fogva figyeli és gyűjti az adatokat. Az alábbi képernyő egy példa a kimenetre. Vegye figyelembe, hogy figyelmeztetéseket fog látni a PowerShell-ablakban, amennyiben nem teljesítette az előző szakaszokban vázolt összes előfeltételt. Mindenképp végezze el ezeket a követelményeket [itt](active-directory-aadconnect-health-agent-install.md#requirements), mielőtt folytatná az ügynök telepítését. 

![Az Azure AD Connect Health for AD DS ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Az ügynök telepítésének ellenőrzéséhez nyissa meg a szolgáltatásokat, és keresse a következőket:

- Azure AD Connect Health AD DS Insights szolgáltatás
- Azure AD Connect Health AD DS Monitoring szolgáltatás

Ez a két szolgáltatás nem indul el addig, amíg a konfigurálást be nem fejezte.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Az Azure AD Connect Health Agent for AD DS telepítése Server Core telepítésen. 
A .exe fájl telepítése után a regisztrációs folyamatot a következő PowerShell-parancs használatával hajthatja végre:

`Register-AzureADConnectHealthADDSAgent -Credentials $cred

## Azure AD Connect Health-ügynökök konfigurálása HTTP proxyk használatára
Az Azure AD Connect Health-ügynököket konfigurálhatja úgy, hogy HTTP proxyval működjenek.

>[AZURE.NOTE]
- A „Netsh WinHttp set ProxyServerAddress” használata nem működik, mivel az ügynök a System.Net használatával adja le a webes kérelmeket, és nem a Microsoft Windows HTTP szolgáltatásokkal.
- A konfigurált Http proxy cím lesz használva a titkosított Https üzenetek átengedésére.
- A hitelesített proxyk (HTTPBasic használatával) nem támogatottak.

### Állapotügynök proxykonfigurációjának módosítása
Az alábbi beállítások használhatóak az Azure AD Connect Health-ügynökök konfigurálásához a HTTP proxyk használatára.

>[AZURE.NOTE] A proxybeállítások frissítéséhez újra kell indítania az összes Azure AD Connect Health-ügynök szolgáltatást. Futtassa az alábbi parancsot:<br>
    Restart-Service AdHealth*

#### Meglévő proxybeállítások importálása

##### Importálás Internet Explorerből
Importálhatja az Internet Explorer HTTP-proxybeállításait, és azokat is használhatja az Azure AD Connect Health-ügynökökhöz. Ehhez futtassa a következő PowerShell-parancsot az állapotügynököt futtató összes kiszolgálón.

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importálás WinHTTP-ből
A WinHTTP-proxybeállítások importálásához futtassa a következő PowerShell-parancsot az állapotügynököt futtató összes kiszolgálón.

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Proxycímek manuális megadása
A proxykiszolgáló manuális megadásához futtassa a következő PowerShell-parancsot az állapotügynököt futtató összes kiszolgálón.

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Példa: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- Az „address” lehet egy, a DNS által feloldható kiszolgálónév vagy egy IPv4-cím
- A „port” elhagyható. Ilyen esetben a 443 lesz az alapértelmezett port.

#### Meglévő proxykonfiguráció törlése
A meglévő proxykonfigurációt a következő parancs futtatásával törölheti.

    Set-AzureAdConnectHealthProxySettings -NoProxy


### Meglévő proxybeállítások olvasása
A jelenleg konfigurált proxybeállításokat a következő parancs használatával olvashatja.

    Get-AzureAdConnectHealthProxySettings


## Az Azure AD Connect Health szolgáltatás kapcsolódásának tesztelése
Előfordulhat, hogy hibák lépnek fel, amelyek hatására az Azure AD Connect Health-ügynök elveszíti a kapcsolatot az Azure AD Connect Health szolgáltatással.  Ezek hálózati, engedélyekkel kapcsolatos és egyéb különféle hibák lehetnek.

Ha a ügynök több mint 2 órán keresztül képtelen adatokat küldeni az Azure AD Connect Health szolgáltatásnak, egy riasztás jelenik meg, és jelzi, hogy „Az üzemállapot-figyelő szolgáltatás adatai nem naprakészek”.  Amennyiben ez történne, most tesztelheti, hogy az Azure AD Connect Health-ügynökök képesek-e adatokat feltölteni az Azure AD Connect Health szolgáltatásba. Ehhez futtassa a következő PowerShell-parancsot azon a gépen, amelyiken az ügynök problémát jelzett.

    Test-AzureADConnectHealthConnectivity -Role Adfs

A szerepkör-paraméter a következő értékeket veheti:

- Adfs
- Sync
- ADDS

A parancs -ShowResults jelzőjével megtekintheti a részletes naplókat.  Használja a következő példát:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]A kapcsolódási eszköz használatához először el kell végeznie az ügynök regisztrációját.  Amennyiben nem tudja elvégezni az ügynök regisztrációját, bizonyosodjon meg róla, hogy az Azure AD Connect Health összes [követelményének](active-directory-aadconnect-health-agent-install.md#requirements) megfelel.  A kapcsolódási teszt végrehajtása alapértelmezés szerint megtörténik az ügynök regisztrációja során.



## Kapcsolódó hivatkozások

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)](active-directory-aadconnect-health-version-history.md)



<!--HONumber=sep16_HO1-->


