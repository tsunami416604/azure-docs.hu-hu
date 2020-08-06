---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: abnarain
ms.openlocfilehash: 49d173e0d0f2b96c385b4325335483d25e9a7c2d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800713"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory saját üzemeltetésű integrációs moduljának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Saját üzemeltetésű IR-naplók összegyűjtése Azure Data Factory

A saját üzemeltetésű IR/Shared IR-ben futó sikertelen tevékenységek esetében Azure Data Factory támogatja a hibanapló megtekintését és feltöltését. A hibajelentési azonosító lekéréséhez kövesse az alábbi lépéseket, majd adja meg a jelentés AZONOSÍTÓját a kapcsolódó ismert problémák megkereséséhez.

1. Ugrás a **tevékenység-futtatások** lapra.

1. A **hiba** oszlopban kattintson az alábbi gombra.

    ![Tevékenység-futtatások lapja](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Megjelenik a sikertelen tevékenység futtatásához kapcsolódó naplók. További segítségért kattintson a **naplók küldése** gombra.

    ![Naplók küldése](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Kiválaszthatja azokat a naplókat, amelyeket el szeretne küldeni. A *saját*üzemeltetésű integrációs modul esetében feltölthet olyan naplókat, amelyek a sikertelen tevékenységgel vagy a saját üzemeltetésű IR-csomóponton lévő összes naplóval kapcsolatosak. A *megosztott IR*esetében csak a sikertelen tevékenységhez kapcsolódó naplók tölthetők fel.

    ![Naplók kiválasztása](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. A naplók feltöltésekor őrizze meg a jelentés AZONOSÍTÓjának rekordját, ha további segítségre van szüksége a probléma megoldásához.

    ![Naplók feltöltése](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> A naplózási és a feltöltési kérelmeket az összes online saját üzemeltetésű IR-példányon végrehajtja a rendszer. Győződjön meg arról, hogy az összes saját üzemeltetésű IR-példány online állapotban van, ha a hiányzó naplók. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Saját üzemeltetésű IR általános hiba vagy hiba

### <a name="tlsssl-certificate-issue"></a>TLS/SSL-tanúsítvány hibája

#### <a name="symptoms"></a>Hibajelenségek

Ha a TLS/SSL-tanúsítványt (Advanced) szeretné engedélyezni a saját üzemeltetésű **IR-Configuration Manager**az  ->  **intranetről**, a TLS/SSL-tanúsítvány kiválasztása után, az alábbi hiba jelenik meg:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

A fenti esetben a felhasználó az utolsó elemként a "microsoft.com" nevű tanúsítványt használja.

#### <a name="cause"></a>Ok

Ez egy ismert probléma a WCF-ben: a WCF TLS/SSL-ellenőrzés csak a SAN-ban lévő utolsó DNSName ellenőrzi. 

#### <a name="resolution"></a>Feloldás

A helyettesítő tanúsítvány támogatott a Azure Data Factory v2 saját üzemeltetésű IR-ben. Ez a probléma általában azért fordul elő, mert az SSL-tanúsítvány nem megfelelő. A SAN-beli utolsó DNSName érvényesnek kell lennie. Kövesse az alábbi lépéseket az ellenőrzéshez. 
1.  Nyissa meg a felügyeleti konzolt, és a tanúsítvány részletei között kattintson a *tulajdonos* és a *tulajdonos alternatív neve* elemre. A fenti esetben például a *tulajdonos alternatív neve*("DNS Name = Microsoft.com.com") utolsó eleme nem legitim.
2.  A hibás DNS-név eltávolításához vegye fel a kapcsolatot a tanúsítvány kiállító vállalatával.

### <a name="concurrent-jobs-limit-issue"></a>Az egyidejű feladatok korlátja probléma

#### <a name="symptoms"></a>Hibajelenségek

Amikor a Azure Data Factory felhasználói felületen korlátozza az egyidejű feladatok korlátját, a rendszer örökre *frissítésként* lefagy.
Az egyidejű feladatok maximális értéke 24 értékre van állítva, és meg szeretné emelni a számot, hogy a feladatok gyorsabban fussanak. A megadható minimális érték 3, a megadható maximális érték pedig 32. Az értéket 24 – 32 értékre növelte, és a *frissítés* gombra kattintva a felhasználói felületen megakadt *a frissítés, ahogy az* alábbiakban látható. A frissítés után az ügyfél továbbra is 24-ként látta el az értéket, és soha nem frissült 32-ra.

![Frissítés állapota](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Ok

A beállításra vonatkozó korlátozás a számítógép logicCore és a memóriától függ, egyszerűen beállíthatja egy kisebb értékre (például 24), és megtekintheti az eredményt.

> [!TIP] 
> - A logikai alapértékek számáról és a gép logikai alapszámának megkereséséről [ebben a cikkben](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)talál további információkat.
> - A Math. log kiszámításával kapcsolatos további információkért tekintse meg [ezt a cikket](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Saját üzemeltetésű IR HA SSL-tanúsítvány probléma

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű IR-munkacsomópont a következő hibát jelezte:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Ok

Az SSL/TLS-kézfogással kapcsolatos esetek kezelésekor előfordulhat, hogy a tanúsítványlánc ellenőrzésével kapcsolatos problémák merülhetnek fel. 

#### <a name="resolution"></a>Feloldás

- Íme egy gyors és intuitív módszer az X. 509 tanúsítványlánc-létrehozási hiba megoldásához.
 
    1. Exportálja a tanúsítványt, amelyet ellenőrizni kell. Lépjen a számítógép-tanúsítvány kezelése elemre, és keresse meg az ellenőriznie kívánt tanúsítványt, és kattintson a jobb gombbal az **összes feladat**  ->  **Exportálás**lehetőségre.
    
        ![Tevékenységek exportálása](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Másolja az exportált tanúsítványt az ügyfélszámítógépre. 
    3. Az ügyfél oldalán futtassa az alábbi parancsot a CMD-ben. Győződjön meg arról, hogy az alábbi *\<certificate path>* és *\<output txt file path>* a helyőrzők a kapcsolódó elérési utakkal lettek lecserélve.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Például:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Ellenőrizze, hogy van-e hiba a kimeneti txt-fájlban. A hiba összegzése a txt-fájl végén található.

        Például: 

        ![Hiba összegzése](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Ha nem lát hibát a naplófájl végén a lent látható módon, azt is megteheti, hogy a tanúsítványlánc sikeresen felépítve lett az ügyfélszámítógépen.
        
        ![Nincs hiba a naplófájlban](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Ha az AIA, a CDP és az OCSP konfigurálva van a tanúsítványfájl-fájlban. A szolgáltatás intuitív módon is ellenőrizhető.
 
    1. Ezt az információt a tanúsítvány részleteinek ellenőrzésével kérheti le.
    
        ![Tanúsítvány részletei](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Futtassa az alábbi parancsot. Győződjön meg arról, hogy *\<certificate path>* a helyőrzőt a tanúsítvány kapcsolódó elérési útjára cserélte.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Ezután megnyílik az **URL-lekérési eszköz** . Az AIA, a CDP és az OCSP tanúsítványait a **beolvasás** gombra kattintva ellenőrizheti.

        ![Lekérés gomb](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        A tanúsítványlánc sikeresen felépíthető, ha az AIA tanúsítványa "ellenőrzött", és a CDP vagy az OCSP tanúsítványa "ellenőrizve".

        Ha az AIA és a CDP beolvasása során hiba lép fel, akkor működjön együtt a hálózati csapattal, hogy az ügyfélszámítógép készen álljon a cél URL-címhez való csatlakozásra. Elég lesz, ha a http-elérési út vagy az LDAP-elérési út ellenőrizhető.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>A saját üzemeltetésű IR nem tudja betölteni a fájlt vagy a szerelvényt

#### <a name="symptoms"></a>Hibajelenségek

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Például: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Ok

Ha folyamat-figyelőt használ, a következő eredményt láthatja:

[![Folyamat figyelője](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Az alábbi képernyőképen látható módon állíthatja be a szűrőt.
> Azt mondja nekünk, hogy a dll- **rendszer. a ValueTuple** nem a GAC-hoz kapcsolódó mappában, vagy a *c:\Program Files\microsoft Integration Runtime\4.0\Gateway*vagy a *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* mappában található.
> Alapvetően a rendszer először a *GAC* -mappából tölti be a DLL-t, majd a *megosztott* és végül az *átjáró* mappából. Ezért a DLL-t bármely olyan elérési útra helyezheti el, amely hasznos lehet.

![Szűrők beállítása](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Feloldás

Megtalálhatja, hogy a **System.ValueTuple.dll** a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* mappában található. A probléma megoldásához másolja a **System.ValueTuple.dll** a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* mappába.

Ugyanezt a módszert használhatja más fájlok vagy szerelvények hiányzó problémáinak megoldásához.

#### <a name="more-information"></a>További információ

Annak oka, hogy a *%windir%\Microsoft.NET\assembly* és a *%windir%\assembly* alatti System.ValueTuple.dll látható, hogy .net-viselkedés. 

Az alábbi hibaüzenetből tisztán láthatja a szerelvény *rendszerét. a ValueTuple* nem létezik. Így ez a probléma akkor fordul elő, amikor az alkalmazás megpróbálja megnézni a szerelvény *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
A GAC-val kapcsolatos további információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Hiányzó saját üzemeltetésű IR-kulcs naplózása

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű integrációs modul hirtelen offline állapotba kerül a kulcs nélkül, az Eseménynaplóban az alábbi hibaüzenet jelenik meg:`Authentication Key is not assigned yet`

![Hiányzó hitelesítési kulcs](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Ok

- A saját üzemeltetésű IR-csomópont vagy logikai saját üzemeltetésű integrációs modul törölve van a portálon.
- A rendszer tiszta eltávolítást végez.

#### <a name="resolution"></a>Feloldás

Ha a fenti okok egyike sem érvényes, akkor nyissa meg a következő mappát: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*, és ellenőrizze, hogy a rendszer törli-e a **konfigurációk** nevű fájlt. Ha törölve van, kövesse az [itt](https://www.netwrix.com/how_to_detect_who_deleted_file.html) található utasításokat a fájl törlésének naplózásához.

![Konfigurációs fájl keresése](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>A saját üzemeltetésű integrációs modul nem használható két helyszíni adattárolók áthidalása érdekében

#### <a name="symptoms"></a>Hibajelenségek

Miután létrehozta a saját üzemeltetésű IRs-t a forrás-és a célhelyek számára, a két IRs-t össze szeretné kapcsolni a másolás befejezéséhez. Ha az adattárak különböző virtuális hálózatok vannak konfigurálva, vagy nem tudják megérteni az átjáró mechanizmusát, a következőhöz hasonló hibák lépnek fel: *a forrás illesztőprogramja nem található a cél IR-ben*; *a cél IR nem fér hozzá a forráshoz*.
 
#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul egy másolási tevékenység központi csomópontjának lett kialakítva, nem pedig az egyes adattárokhoz telepítendő ügyfél-ügynöknek.
 
A fenti esetben az egyes adattárokhoz tartozó társított szolgáltatást ugyanazzal az IR-vel kell létrehozni, és az IR-nek képesnek kell lennie az adattárak elérésére a hálózaton keresztül. Függetlenül attól, hogy az IR telepítve van-e a forrás adattárral, a rendeltetési adattárral vagy egy harmadik gépen, ha két társított szolgáltatás eltérő IRs-vel lett létrehozva, de ugyanabban a másolási tevékenységben használják, a cél IR lesz használatban, és mindkét adattár illesztőprogramjait telepíteni kell a cél IR-gépen.

#### <a name="resolution"></a>Feloldás

Telepítse az illesztőprogramokat a forrás és a cél számára a cél IR-re, és győződjön meg arról, hogy az hozzáfér a forrás adattárolóhoz.
 
Ha a forgalom nem haladhat át a hálózaton két adattár között (például két virtuális hálózatok van konfigurálva), akkor előfordulhat, hogy a másolást nem fejezi be egyetlen tevékenységben, még az IR-vel is. Ebben az esetben két másolási tevékenységet hozhat létre két IRs-vel, amelyek mindegyike egy, az 1. adattárból az Azure Blob Storageba másolható, egy másik pedig az Azure Blob Storageról a 2. adattárba való másoláshoz. Ez szimulálhatja azt a követelményt, hogy az IR használatával olyan hidat hozzon létre, amely összekapcsolja a két leválasztott adattárat.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>A hitelesítő adatok szinkronizálásának hibája miatt a hitelesítő adat elvész

#### <a name="symptoms"></a>Hibajelenségek

A "XXXXXXXXXX" adatforrás hitelesítő adatait a rendszer törli az aktuális Integration Runtime-csomópontról a hasznos adatokkal, ha törli a kapcsolati szolgáltatást a Azure Portalon, vagy ha a feladat nem megfelelő adattartalommal rendelkezik, hozzon létre egy új link Service-t a hitelesítő adatokkal.

#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul két csomóponttal, de nem a hitelesítő adatok szinkronizálási állapotában van kiépítve, ami azt jelenti, hogy a diszpécser csomópontban tárolt hitelesítő adatok nem szinkronizálhatók más munkavégző csomópontokkal. Ha bármely feladatátvétel a kiosztó csomópontról a feldolgozó csomópontra történik, de a hitelesítő adatok csak a korábbi diszpécser csomópontban léteztek, a feladat sikertelen lesz, amikor a hitelesítő adatokhoz próbál hozzáférni, és a fenti hibaüzenetet fogja érinteni.

#### <a name="resolution"></a>Feloldás

A probléma elkerülésének egyetlen módja annak biztosítása, hogy két csomópont legyen hitelesítő adatok szinkronizálási állapota. Ellenkező esetben az új kiosztó hitelesítő adatait újra be kell jelentkeznie.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Hiányzó titkos kulcs miatt nem lehet kijelölni a tanúsítványt

#### <a name="symptoms"></a>Hibajelenségek

1.  PFX-fájl importálása a tanúsítványtárolóba.
2.  Ha az IR Configuration Manager felhasználói felületen keresztül kiválasztja a tanúsítványt, a következő hibaüzenet jelenik meg:

    ![Hiányzó titkos kulcs](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Ok

- A felhasználói fiók alacsony jogosultsággal rendelkezik, és nem fér hozzá a titkos kulcshoz.
- A tanúsítvány aláírásként lett létrehozva, de nem a Key Exchange.

#### <a name="resolution"></a>Feloldás

1.  Olyan Kiemelt fiókot használjon, amely hozzáfér a titkos kulcshoz a felhasználói felület működtetéséhez.
2.  A tanúsítvány importálásához futtassa az alábbi parancsot:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Saját üzemeltetésű IR-beállítás

### <a name="the-integration-runtime-registration-error"></a>A Integration Runtime regisztrációs hiba 

#### <a name="symptoms"></a>Hibajelenségek

Néha a saját üzemeltetésű integrációs modult egy másik fiókban szeretnénk futtatni az alábbi okokból:
- A vállalati házirend nem engedélyezi a szolgáltatásfiókot.
- Némi hitelesítés szükséges.

Miután módosította a szolgáltatásfiókot a szolgáltatás paneljén, előfordulhat, hogy a Integration Runtime működése leáll.

![IR-regisztrációs hiba](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Ok

Számos olyan erőforrás van, amely csak a szolgáltatásfiók számára van megadva. A szolgáltatásfiók másik fiókra való módosításakor az összes függő erőforrás engedélye változatlan marad.

#### <a name="resolution"></a>Feloldás

A hiba vizsgálatához lépjen a Integration Runtime eseménynaplóba.

![IR-Eseménynapló](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Ha a hiba a fenti *UnauthorizedAccessException*jelenik meg, kövesse az alábbi utasításokat:


1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.

    ![Bejelentkezési szolgáltatásfiók](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Győződjön meg arról, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e az R/W engedéllyel a (z) *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*mappában.

    - Alapértelmezés szerint, ha a szolgáltatás bejelentkezési fiókja nem módosult, akkor az R/W engedélyének kell lennie.

        ![Szolgáltatás engedélye](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Ha módosította a szolgáltatás bejelentkezési fiókját, kövesse az alábbi lépéseket a probléma enyhítéséhez:
        1. Törölje a jelenlegi saját üzemeltetésű integrációs modul eltávolítását.
        1. Telepítse a saját üzemeltetésű IR-biteket.
        1. A szolgáltatásfiók módosításához kövesse az alábbi utasításokat: 
            1. Nyissa meg a selfhosted IR telepítési mappáját, váltson a következő mappára: *Microsoft Integration Runtime\4.0\Shared*.
            1. Indítsa el a parancssort emelt szintű jogosultság használatával. Cserélje *\<user>* le *\<password>* a és a nevet a saját felhasználónevére és jelszavára, majd futtassa az alábbi parancsot:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Ha a LocalSystem fiókra szeretne váltani, ügyeljen arra, hogy megfelelő formátumot használjon ehhez a fiókhoz. Az alábbi példa a megfelelő formátumot mutatja be:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Ne **használja a** formátumot az alább látható módon:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Alternatív megoldásként, mivel a helyi rendszer magasabb jogosultsággal rendelkezik, mint a rendszergazda, közvetlenül is megváltoztathatja azt a "szolgáltatások" szolgáltatásban.
            1. Használhat helyi/tartományi felhasználót az IR szolgáltatás bejelentkezési fiókjához.            
        1. Regisztrálja a Integration Runtime.

Ha a hiba a következőképpen jelenik *meg: "Integration Runtime szolgáltatás" (DIAHostService) indítása sikertelen. Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások indításához*, kövesse az alábbi utasításokat:

1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.
   
    ![Bejelentkezési szolgáltatásfiók](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Győződjön meg arról, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e a Windows-szolgáltatás indításához szükséges **Bejelentkezés szolgáltatásként** :

    ![Bejelentkezés szolgáltatásként](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>További információ

Ha a fenti két minta egyike sem vonatkozik a megoldásra, próbálja meg összegyűjteni a következő Windows-eseménynaplókat: 
- Alkalmazások és szolgáltatások naplói – > Integration Runtime
- Windows-naplók – > alkalmazás

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Nem található a regisztráció gomb a saját üzemeltetésű integrációs modul regisztrálásához    

#### <a name="symptoms"></a>Hibajelenségek

A **regisztráció** gomb nem található a Configuration Manager felhasználói felületen a saját üzemeltetésű integrációs modul regisztrálásakor.

![Nincs regisztráció gomb](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Ok

A *Integration Runtime 3,0*-es verziójának megjelenése óta egy meglévő Integration Runtime csomóponton található **regisztráció** gomb el lett távolítva a tisztább és biztonságosabb környezet lehetővé tételéhez. Ha egy csomópont valamilyen Integration Runtime van regisztrálva (akár online, akár nem), akkor a másik Integration Runtimeba való újbóli regisztrálásához el kell távolítania az előző csomópontot, majd telepítenie és regisztrálnia kell a csomópontot.

#### <a name="resolution"></a>Feloldás

1. Nyissa meg a Vezérlőpultot a meglévő Integration Runtime eltávolításához.

    > [!IMPORTANT] 
    > Az alábbi eljárásban válassza az Igen lehetőséget. Ne tartsa meg az adattárolást az eltávolítási folyamat során.

    ![Adat törlése](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Ha nem rendelkezik az Integration Runtime telepítő MSI-vel, lépjen a [letöltőközpontban](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) a legújabb Integration Runtime letöltéséhez.
1. Telepítse az MSI-t, és regisztrálja a Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Nem lehet regisztrálni a saját üzemeltetésű IR-t a localhost miatt    

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet regisztrálni a saját üzemeltetésű integrációs modult egy új gépre get_LoopbackIpOrNamekor.

**Hibakeresés:** Futásidejű hiba történt.
A "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" típusú inicializáló kivételt váltott ki.
Nem helyreállítható hiba történt egy adatbázis-keresés során.
 
**Kivétel részletei:** System. TypeInitializationException: a "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" típus inicializáló kivételt váltott ki. ---> System .net. Sockets. SocketException: A rendszer nem helyreállítható hibát észlelt a System .net. DNS. GetAddrInfo (karakterlánc neve) adatbázisban való keresése során.

#### <a name="cause"></a>Ok

A probléma általában a localhost feloldásakor történik.

#### <a name="resolution"></a>Feloldás

A localhost 127.0.0.1 használata a fájl futtatásához és a probléma megoldásához.


### <a name="self-hosted-setup-failed"></a>A saját üzemeltetésű telepítés nem sikerült    

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet eltávolítani egy meglévő IR-t, vagy új IR-t telepíteni vagy frissíteni egy meglévő IR-t egy új IR-re.

#### <a name="cause"></a>Ok

A telepítés a Windows Installer szolgáltatástól függ. Lehetséges, hogy a telepítési problémát a következők okozhatják:
- Nincs elég szabad lemezterület
- Engedélyek hiánya
- Az NT szolgáltatás valamilyen okból zárolva van
- A CPU-kihasználtság túl magas
- Az MSI-fájl lassú hálózati helyen van tárolva
- Egyes rendszerfájlok vagy kibocsátásiegység-forgalmi jegyzékek véletlenül lettek megérintve


## <a name="self-hosted-ir-connectivity-issues"></a>Saját üzemeltetésű IR-kapcsolati problémák

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>A saját üzemeltetésű Integration Runtime nem tud kapcsolódni a Cloud Service-hez

#### <a name="symptoms"></a>Hibajelenségek

![Saját üzemeltetésű IR-kapcsolati probléma](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrációs modul nem tud kapcsolódni a Data Factory szolgáltatáshoz (háttér). Ezt a problémát általában a tűzfal hálózati beállításai okozzák.

#### <a name="resolution"></a>Feloldás

1. Győződjön meg arról, hogy fut-e az Integration Runtime szolgáltatás.
    
   ![A saját üzemeltetésű IR-szolgáltatás futási állapota](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Ha a szolgáltatás fut, folytassa a 3. lépéssel.

1. Ha nincs proxy konfigurálva a saját üzemeltetésű integrációs modulban (amely az alapértelmezett beállítás), futtassa a következő PowerShell-parancsot azon a gépen, amelyen a saját üzemeltetésű integrációs modul telepítve van:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A szolgáltatás URL-címe eltérő lehet a Data Factory helyétől függően. A szolgáltatás URL-címét az **ADF UI**  >  **Connections**  >  **Integration Runtimes**saját üzemeltetésű  >  **IR**  >  -**csomópontok**  >  **megtekintése szolgáltatás URL-** címének szerkesztése szakaszban találja.
            
    A várt válasz a következő:
            
    ![PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Ha nem kapja meg a várt választ, használja a következő módszerek egyikét az adott helyzetnek megfelelően:
            
    * Ha "a távoli név nem oldható fel" üzenet jelenik meg, a tartománynévrendszer (DNS) problémája van. A probléma megoldásához forduljon a hálózati csapathoz.
    * Ha "SSL/TLS-tanúsítvány nem megbízható" üzenetet kap, ellenőrizze, hogy a tanúsítvány megbízható-e https://wu2.frontend.clouddatahub.net/ a gépen, majd telepítse a nyilvános tanúsítványt a Tanúsítványkezelő használatával. A műveletnek csökkentenie kell a problémát.
    * Nyissa meg a **Windows**  >  **Eseménynapló (naplók)**  >  **alkalmazásait és szolgáltatásait tartalmazó naplókat**  >  **Integration Runtime** , és keresse meg a DNS, a tűzfalszabály vagy a vállalati hálózati beállítások által okozott hibát. (Ha ilyen hibát talál, kényszerítse a kapcsolatok bezárását.) Mivel minden vállalat testreszabott hálózati beállításokat tartalmaz, a problémák elhárításához forduljon a hálózati csapatához.

1. Ha a "proxy" konfigurálva van a saját üzemeltetésű integrációs modulban, ellenőrizze, hogy a proxykiszolgáló hozzáférhet-e a szolgáltatási végponthoz. A minta parancsokért lásd: [PowerShell, webes kérelmek és proxyk](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A várt válasz a következő:
            
![2. PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxyval kapcsolatos megfontolások:
> *    Győződjön meg arról, hogy a proxykiszolgálót a biztonságos címzettek listára kell helyezni. Ha igen, győződjön meg arról, hogy [ezek a tartományok](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) a biztonságos címzettek listán vannak.
> *    Győződjön meg arról, hogy a "wu2.frontend.clouddatahub.net/" TLS/SSL-tanúsítvány megbízható-e a proxykiszolgálón.
> *    Ha Active Directory hitelesítést használ a proxyn, módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely a proxyt "Integration Runtime szolgáltatás" néven éri el.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul csomópontja/logikai a (z) rendszer inaktív/"fut (korlátozott)" állapotban van.

#### <a name="cause"></a>Ok 

Előfordulhat, hogy a saját üzemeltetésű integrált futásidejű csomópont **inaktív** állapotú, ahogy az alábbi képernyőképen is látható:

![Inaktív önkiszolgáló IR-csomópont](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ez a viselkedés akkor fordul elő, ha a csomópontok nem tudnak kommunikálni egymással.

#### <a name="resolution"></a>Feloldás

1. Jelentkezzen be a csomópont által üzemeltetett virtuális gépre. Az **alkalmazások és szolgáltatások naplóiban**  >  **Integration Runtime**, nyissa meg Eseménynapló, és szűrje az összes hibát.

1. Győződjön meg arról, hogy a hibanapló a következő hibát tartalmazza-e: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Ha ezt a hibát látja, futtassa a következő parancsot a parancssorban: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Ha a következő hibaüzenetet kapja, lépjen kapcsolatba az informatikai részleggel a probléma megoldása érdekében. A sikeres Telnet után lépjen kapcsolatba Microsoft ügyfélszolgálata, ha továbbra is problémái vannak az integratív futtatókörnyezet csomópontjának állapotával.
        
   ![Parancssori hiba](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Győződjön meg arról, hogy a hibanapló tartalmazza-e a következőket:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. A probléma megoldásához próbálja meg a következő módszerek egyikét vagy mindkettőt:
    - Helyezze az összes csomópontot ugyanabba a tartományba.
    - Adja hozzá az IP-címet a gazdagépek leképezéséhez az összes üzemeltetett virtuális gép gazdagépének fájljaiban.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Kapcsolódási probléma a saját üzemeltetésű IR és Data Factory vagy a saját üzemeltetésű IR és adatforrás/fogadó között

A hálózati kapcsolat problémájának megoldásához tudnia kell, hogyan [gyűjtheti a hálózati nyomkövetést](#how-to-collect-netmon-trace), hogyan használhatja azt, és [elemezheti a netmon nyomkövetést](#how-to-analyze-netmon-trace) , mielőtt a netmon-eszközöket valós esetekben alkalmazza a saját üzemeltetésű integrációs modulból.

#### <a name="symptoms"></a>Hibajelenségek

Időnként, ha a kapcsolódási problémákat (például az alábbiakat) a saját üzemeltetésű integrációs modul és a Data Factory között elhárítja: 

![HTTP-kérelem sikertelen](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Vagy a saját üzemeltetésű IR és adatforrás/fogadó között a következő hibákat tapasztaljuk:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Megoldás:

A fenti problémák megoldásakor a további hibaelhárításhoz tekintse meg a következő utasításokat:

Végezze el a netmon nyomkövetést, és elemezze tovább.
- Először beállíthatja a szűrőt úgy, hogy a kiszolgálóról az ügyfél felé irányuló alaphelyzetbe állítson. Az alábbi példában látható, hogy a kiszolgáló oldalon Data Factory kiszolgáló található.

    ![A adatfeldolgozó kiszolgáló](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- A csomag alaphelyzetbe állítása után a következő TCP-vel keresheti meg a beszélgetést.

    ![Beszélgetés keresése](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Ezután a szűrő eltávolításával lekérheti az ügyfél és Data Factory kiszolgáló közötti konverziót.

    ![Beszélgetés beolvasása](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Az összegyűjtött netmon-nyomkövetés alapján megtudhatjuk, hogy a TTL (TimeToLive) összesen 64. A [cikkben](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) említett **alapértelmezett TTL-és ugrási határértékek** (az alább leírtak szerint) alapján láthatjuk, hogy ez a Linux rendszer, amely alaphelyzetbe állítja a csomagot, és a leválasztást okoz.

    Az alapértelmezett TTL-és ugrás-határértékek eltérőek lehetnek a különböző operációs rendszerek között:
    - Linux kernel 2,4 (kb. 2001): 255 TCP, UDP és ICMP rendszerhez
    - Linux kernel 4,10 (2015): 64 TCP, UDP és ICMP rendszerhez
    - Windows XP (2001): 128 TCP, UDP és ICMP esetén
    - Windows 10 (2015): 128 TCP, UDP és ICMP esetén
    - Windows Server 2008:128 TCP, UDP és ICMP esetén
    - Windows Server 2019 (2018): 128 TCP, UDP és ICMP rendszerhez
    - macOS (2001): 64 a TCP, az UDP és az ICMP protokollhoz

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Azonban a fenti példában a 64 helyett 61 jelenik meg, mert ha a hálózati csomag eléri a célhelyet, a különböző ugrásokra, például útválasztók/hálózati eszközökre kell mutatnia. Az útválasztók/hálózati eszközök száma a végső TTL-ben lesz levonva.
    Ebben az esetben láthatjuk, hogy az Alaphelyzetbe állítás a 64-es TTL-vel rendelkező Linux rendszerből is elvégezhető.

- A negyedik ugrást a saját üzemeltetésű IR-ből kell ellenőrizni annak ellenőrzéséhez, hogy az eszköz alaphelyzetbe állítása honnan származhat.
 
    *Hálózati csomag az A Linux rendszertől a TTL 64-> B TTL 64 mínusz 1 = 63-> C TTL 63 mínusz 1 = 62-> TTL 62 mínusz 1 = 61 saját üzemeltetésű IR*

- Ideális esetben a TTL 128 lesz, ami azt jelenti, hogy a Windows rendszer fut a Data Factory. Ahogy az alábbi példában is látható, a *128 – 107 = 21 ugrás*, ami azt jelenti, hogy a csomaghoz tartozó 21 ugrást a rendszer a TCP 3-kézfogás során Data Factory a saját üzemeltetésű IR-be küldi.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Ezért be kell vonnia a hálózati csapatot annak ellenőrzéséhez, hogy a negyedik Ugrás a saját üzemeltetésű integrációs modulból származik-e. Ha a tűzfal Linux rendszer, akkor ellenőrizze, hogy az eszköz miért állítja vissza a csomagot a TCP 3 kézfogás után. Ha azonban nem tudja, hol kell megvizsgálnia a vizsgálatot, próbálja meg a netmon-nyomkövetést a saját üzemeltetésű integrációs modulból és a tűzfalból összekapcsolni a problémás idő alatt, hogy kiderítse, melyik eszköz állíthatja alaphelyzetbe ezt a csomagot Ebben az esetben arra is szükség van, hogy a hálózati csapatot tovább folytassa.

### <a name="how-to-collect-netmon-trace"></a>Netmon-nyomkövetés gyűjtése

1.  Töltse le a netmon-eszközöket [erről a webhelyről](https://www.microsoft.com/en-sg/download/details.aspx?id=4865), és telepítse azt a kiszolgálói gépre (a problémával rendelkező kiszolgálóra) és az ügyfélre (például saját üzemeltetésű IR-re).

2.  Hozzon létre egy mappát, például a következő elérési úton: *D:\netmon*. Győződjön meg arról, hogy elegendő lemezterülettel rendelkezik a napló mentéséhez.

3.  Az IP-cím és a port adatainak rögzítése. 
    1. Indítson el egy parancssort.
    2. Válassza a Futtatás rendszergazdaként lehetőséget, és futtassa a következő parancsot:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Rögzítse a netmon nyomkövetését (hálózati csomag).
    1. Indítson el egy parancssort.
    2. Válassza a Futtatás rendszergazdaként lehetőséget, és futtassa a következő parancsot:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. A hálózat lap rögzítéséhez három különböző parancsot használhat:
        - A: RoundRobin file parancs (ez csak egy fájlt fog rögzíteni, és felülírja A régi naplókat).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - B. lehetőség: láncolt fájl parancs (ez az új fájlt fogja létrehozni, ha elérte a 200 MB-ot).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - C. lehetőség: ütemezett fájl parancs.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Nyomja le a **CTRL + C** billentyűkombinációt a netmon nyomkövetés rögzítésének leállításához.
 
> [!NOTE]
> Ha csak a netmon nyomkövetést szeretné összegyűjteni az ügyfélszámítógépen, kérje le a kiszolgáló IP-címét, hogy segítsen a nyomkövetés elemzésében.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-nyomkövetés elemzése

Ha a **8.8.8.8 888** -as netmon-nyomkövetéssel próbálkozik a fent leírtak szerint, az alábbi nyomkövetés látható:

![netmon nyomkövetés 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon nyomkövetés 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Ez azt jelenti, hogy a **888**-es porton alapuló TCP-csatlakozás nem végezhető el a **8.8.8.8** -kiszolgáló oldalára, így két **SynReTransmit** további csomagot láthat. Mivel a Source **HOST2** nem tudott kapcsolódni az **8.8.8.8** -hez az első csomagban, továbbra is a kapcsolódást fogja végezni.

> [!TIP]
> - Kattintson a **szűrő**  ->  **szabványos szűrő**  ->  **címek**  ->  **IPv4-címek**betöltése lehetőségre.
> - Adja meg a bemeneti **IPv4.-címek = = 8.8.8.8** szűrőként, majd kattintson az **alkalmaz**gombra. Ezt követően csak a helyi gépről érkező kommunikáció jelenik meg a cél **8.8.8.8**.

![címek szűrése 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![címek szűrése 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Az alábbi példa egy jó forgatókönyv megjelenését mutatja be. 

- Ha a Telnet **8.8.8.8 53** a probléma nélkül működik, láthatja a TCP 3 kézfogást, majd a munkamenet a TCP 4 kézfogással fejeződik be.

    ![példa az 1. jó forgatókönyvre](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![helyes forgatókönyv 2. példa](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- A fenti TCP 3 kézfogás alapján a következő munkafolyamat látható:

    ![TCP 3 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- A munkamenet és a munkafolyamata befejezéséhez a TCP 4 kézfogás a következőképpen fog megjelenni:

    ![TCP 4 kézfogás](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="self-hosted-ir-sharing"></a>Saját üzemeltetésű integrációs modul megosztása

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>A saját üzemeltetésű integrációs modul megosztása egy másik bérlőn nem támogatott 

#### <a name="symptoms"></a>Hibajelenségek

Előfordulhat, hogy más adatgyárakat (különböző bérlőket) is meg kell hívnia, miközben megpróbálta megosztani a saját üzemeltetésű integrációs modult Azure Data Factory felhasználói felületen, de nem tudja megosztani a saját üzemeltetésű integrációs modult különböző bérlők adatelőállítói között.

#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul nem osztható meg több Bérlővel.


## <a name="next-steps"></a>További lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Adatforgalom teljesítményének feltérképezése útmutató](concepts-data-flow-performance.md)
