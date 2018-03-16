---
title: "Hozzáférés-vezérlés (Java) használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan fejleszthet és hozzáférés-vezérlés használata Javával az Azure-ban."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mtillman
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: b555ef40fae8156d2957643697d6450ef22b215a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Hogyan hitelesítheti az Eclipse használata Azure hozzáférés-vezérlési szolgáltatásban webes felhasználók
Ez az útmutató bemutatja, hogyan használható az Azure Access Control Service (ACS) az Azure-eszközkészlet belül az eclipse-ben. Az ACS további információkért lásd: a [további lépések](#next_steps) szakasz.

> [!NOTE]
> Az Azure Access Services vezérlő szűrő egy közösségi technológiai előzetes. Kiadás előtti szoftverként azt van hivatalosan Microsoft nem támogatja.
> 
> 

## <a name="what-is-acs"></a>Mi az az ACS?
Legtöbb fejlesztő nem identity szakértők és általában nem szeretné töltött idő fejlesztését hitelesítési és engedélyezési mechanizmusok az alkalmazások és szolgáltatások számára. ACS az Azure-szolgáltatások, amelyek egyszerű megoldás a felhasználók, akik a webalkalmazások és szolgáltatások eléréséhez tényező összetett hitelesítés logika a kód nélkül hitelesítésére.

A következő funkciók érhetők el az ACS szolgáltatásban:

* Integráció a Windows Identity Foundation (WIF).
* Beleértve a Windows Live ID, Google, Yahoo!-s és Facebook a népszerű webes Identitásszolgáltatók (IP-címek) támogatása.
* Támogatás az Active Directory összevonási szolgáltatások (AD FS) 2.0-s.
* Egy nyitott Data protokoll (OData)-alapú szolgáltatás, amely szoftveres hozzáférést biztosít az ACS-beállításokat.
* A felügyeleti portál, amely lehetővé teszi az ACS-beállításokat rendszergazdai jogosultságokkal.

ACS kapcsolatos további információkért lásd: [Access Control Service 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Alapelvek
Az Azure ACS a rendszerbiztonsági tagok a jogcímalapú identitás - hitelesítési mechanizmusok helyileg futó alkalmazásokhoz vagy a felhőben létrehozása egységes megközelítésének épül. Jogcímalapú identitás közös biztosítja az alkalmazások és szolgáltatások szerezhető az azonosító információ van szükségük a szervezet más, és az interneten lévő felhasználók számára.

Az útmutató a feladatok végezhetők, tisztában kell lennie a következő fogalmak:

**Ügyfél** -Ez az útmutató útmutató keretében, ez az próbál hozzáférni a webes alkalmazás böngészőt.

**Függő entitásonkénti (RP) alkalmazás** -alkalmazás egy függő Entitás egy webhelyre vagy szolgáltatásba, amely egy külső szolgáltatónak hitelesítési outsources. Az azonosító egy zsargon azt tegyük fel például, hogy a függő Entitás megbízhatóságai adott szolgáltatót. Ez az útmutató ismerteti, hogyan konfigurálhatja a megbízhatósági ACS az alkalmazást.

**Token** -jogkivonat általában egy felhasználó sikeres hitelesítés után kiadott biztonsági adatok gyűjteménye. Tartalmaz egy *jogcímek*, a hitelesített felhasználó attribútuma. A jogcím jelenthet a felhasználó nevét, egy felhasználói szerepkör azonosítójának tartozik, a felhasználó élettartama, és így tovább. A jogkivonat általában digitális aláírással, ami azt jelenti, azt is mindig forrása lehet vissza a kiállítójának, és a benne lévő tartalom ne lehessen illetéktelenül. Egy felhasználó hozzáfér egy függő Entitás alkalmazás segítségével egy szervezet, amely megbízik a függő Entitás alkalmazás által kiállított érvényes jogkivonat.

**Identity Provider (IP)** -egy IP-cím egy szervezet, amely hitelesíti a felhasználói identitások és biztonsági jogkivonatokat bocsát ki. A tényleges munkát jogkivonatok kiállítása történik, ha egy különleges biztonsági jogkivonat szolgáltatás (STS) nevű szolgáltatás. IP-címek tipikus például Windows Live ID, Facebook-on, üzleti felhasználói tárolóhelyekkel (például az Active Directory), és így tovább.
ACS megbízható IP-címre van beállítva, amikor a rendszer elfogadja, és ellenőrizze, hogy az IP által kiállított jogkivonatokat. ACS is megbízhat több IP-cím egyszerre, ami azt jelenti, hogy amikor az alkalmazás ACS megbízhatónak tekinti, azonnal kínálhat az alkalmazás összes IP-címekről a hitelesített felhasználók számára, hogy az ACS megbízik az Ön nevében.

**Összevonási szolgáltató (pi)** -IP-címek közvetlen ismerő felhasználók, és azokat a hitelesítő adataik használatával hitelesíti és mi tudják róluk kapcsolatos jogcímeket kiadni. Egy összevonási szolgáltató (pi) egy eltérő jellegű hatóság: ahelyett, hogy közvetlenül a felhasználók hitelesítéséhez, úgy működik, mint egy közvetítő és a brókerek hitelesítési között egy függő Entitás és egy vagy több IP-címek. IP-címek és a FPs ki biztonsági jogkivonatokat, így mindkettő használja a biztonsági jogkivonat szolgáltatás (STS). ACS egy pi.

**ACS Jogcímszabály-motor** -használatával a megbízható IP-címekről jogkivonatok azt jelentette, hogy a függő Entitás által a bejövő jogkivonatokat logika van szerkezetbe egyszerű jogcím-átalakítási szabályok formájában. ACS funkciókat a jogcímszabály-motor, amely gondoskodik, függetlenül a függő Entitás megadott átalakítása logika alkalmazását.

**Az ACS-Namespace** -egy névtér, a beállítások sorolására használó ACS legfelső szintű partíciójának. Egy névtér, amely tárolja az IP-címek megbízik, a kívánja szolgáltatni RP alkalmazások listáját, a szabályokat, hogy a szabály várt motornak, hogy a bejövő jogkivonatokat feldolgozni, és így tovább. Egy névtér, amely az alkalmazás és a fejlesztői megszerezni az ACS működéséhez használható különböző végpontok mutatja.

Az alábbi ábrán láthatók az ACS-hitelesítés működéséről webes alkalmazásokkal együtt:

![ACS folyamatábrája][acs_flow]

1. Az ügyfél (ebben az esetben egy böngésző) oldal kéri le az erőforrás-Szolgáltatónál.
2. Mivel a kérelem még nem hitelesíthetők, a függő Entitás átirányítja a felhasználót a szolgáltatónak megbízhatónak, amely olyan ACS. Az ACS a felhasználó megadja a függő Entitás számára megadott IP-címek a választható. A felhasználó kiválasztja a megfelelő IP.
3. Az ügyfél az IP-címekhez hitelesítési oldalra jut, és megkérdezi a felhasználót, hogy jelentkezzen be.
4. Az ügyfél hitelesítése (például a hitelesítőadatok meg legyenek adva identitás), az IP-cím kibocsát egy biztonsági jogkivonatot.
5. Után egy biztonsági jogkivonatot kibocsátó, az IP-cím ACS átirányítja az ügyfelet, és az ügyfél elküldi az ACS az Identitásszolgáltató által kiadott biztonsági jogkivonat.
6. ACS ellenőrzi az IP-bemenetek identitásának Ez a token-be az ACS szabálymotor jogcímek kiszámítja a kimeneti identitási jogcímek és kiad egy új biztonsági jogkivonatot, amely tartalmazza a kimeneti jogcímek által kiadott biztonsági jogkivonat.
7. ACS átirányítja az ügyfelet az erőforrás-Szolgáltatónál. Az ügyfél elküldi az új biztonsági jogkivonatot az ACS által kiállított, az erőforrás-Szolgáltatónál. A függő Entitás ellenőrzi az ACS által kiadott biztonsági jogkivonat aláírása, ellenőrzi a jogkivonatában lévő jogcímeket, és visszaadja az eredetileg kért lapot.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató a feladatok elvégzéséhez a következőkre lesz szüksége:

* A Java fejlesztői készlet (JDK), v 1.6 vagy újabb.
* Java EE-fejlesztőknek IDE Holdas Indigo vagy újabb. Ez letölthető <http://www.eclipse.org/downloads/>. 
* A Java-alapú webkiszolgáló vagy a kiszolgáló, például az Apache Tomcat, GlassFish, JBoss alkalmazáskiszolgáló vagy Jetty terjesztési.
* Azure-előfizetéssel, amely szerezhető: a <http://www.microsoft.com/windowsazure/offers/>.
* Az eclipse-ben az Azure eszközkészlet 2014. április kiadás vagy újabb. További információkért lásd: [az eclipse-ben az Azure eszközkészlet telepítésével](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Egy X.509 tanúsítvány az alkalmazás használata. Szüksége lesz a tanúsítvány nyilvános tanúsítvány (.cer), mind a személyes információcsere (. PFX) formátumú. (Ez a tanúsítvány létrehozásához beállítások előnyben, annak leírását az oktatóanyag későbbi részében).
* A tesztkörnyezet a Azure számítási megvitatni emulátor és a központi telepítési módszerek [egy Hello World alkalmazás létrehozása az Azure az eclipse-ben](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Hozzon létre egy ACS-Namespace
Az Azure Access Control Service (ACS) használatának megkezdéséhez létre kell hoznia egy ACS-névtér. A névteret biztosít az alkalmazáson belül az ACS-erőforrások címzéshez egyedi hatókör.

1. Jelentkezzen be a [Azure felügyeleti portálján][Azure Management Portal].
2. Kattintson a **Active Directory**. 
3. Egy új hozzáférés-vezérlés névtér létrehozásához kattintson a **új**, kattintson a **alkalmazásszolgáltatások**, kattintson a **hozzáférés-vezérlés**, és kattintson a **Gyorslétrehozás**. 
4. Adja meg a névtér nevét. Azure ellenőrzi, hogy a név egyedi.
5. Válassza ki a régiót, amelyben a névtér használható. A legjobb teljesítmény érdekében régiót kell használnia, amelyben a az alkalmazás telepítéséhez.
6. Ha egynél több előfizetéssel rendelkezik, válassza ki az ACS-névtér esetében használni kívánt előfizetést.
7. Kattintson a **Create** (Létrehozás) gombra.

Azure hoz létre, és aktiválja a névteret. Várjon, amíg az új névtér állapota **aktív** folytatása előtt. 

## <a name="add-identity-providers"></a>Identitás-szolgáltatóktól hozzáadása
Ebben a feladatban vegye fel a hitelesítéshez RP az alkalmazáshoz használandó IP-címek. Bemutatási célokra Ez a feladat bemutatja, hogyan adja hozzá a Windows Live olyan IP-címet, de az IP-címek az ACS felügyeleti portálon felsorolt bármelyikét használhatja.

1. Az a [Azure felügyeleti portálon][Azure Management Portal], kattintson a **Active Directory**, jelöljön ki egy hozzáférés-vezérlés névteret, és kattintson a **kezelése**. Megnyitja az ACS felügyeleti portálján.
2. Az ACS felügyeleti portál bal oldali navigációs ablaktábláján kattintson **identitás-szolgáltatóktól**.
3. A Windows Live ID alapértelmezés szerint engedélyezve van, és nem törölhető. Ez az oktatóanyag céljából csak az Windows Live ID szolgál. Ezen a képernyőn, azonban ha kattintva hozzáadhatja más IP-címek, a **Hozzáadás** gombra.

A Windows Live ID engedélyezve van, egy IP-Címek használhatók a ACS névterét. Ezt követően adja meg a Java-alkalmazásokra (a később létrehozott), egy függő Entitás.

## <a name="add-a-relying-party-application"></a>Függő entitás alkalmazás felvétele
Ebben a feladatban konfigurálja ACS ismeri fel a Java-webalkalmazás érvényes RP-alkalmazásként.

1. Az ACS felügyeleti portál, kattintson a **függő entitás alkalmazásai számára**.
2. Az a **függő entitás alkalmazásai számára** kattintson **Hozzáadás**.
3. Az a **függő entitás alkalmazás hozzáadása** lapján tegye a következőket:
   
   1. A **neve**, írja be a függő Entitás nevét. Ez az oktatóanyag céljából, írja be a következőt **Azure Web Apps**.
   2. A **mód**, jelölje be **adja meg a beállításokat manuálisan**.
   3. A **tartomány**, írja be az URI, amely az ACS által kiadott biztonsági jogkivonat vonatkozik. Ez a feladat, írja be a következőt **8080 /**.
      ![Függő entitás realm compute emulator használható][relying_party_realm_emulator]
   4. A **visszatérési URL-** URL-címét, amelyhez az ACS a biztonsági jogkivonatot ad vissza. Ez a feladat, írja be a következőt **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![függő entitás URL-cím használható compute emulator adja vissza.][relying_party_return_url_emulator]
   5. Fogadja el a többi mezőt az alapértelmezett értékeket.
4. Kattintson a **Save** (Mentés) gombra.

Ezzel sikeresen beállította a Java-webalkalmazás az Azure compute emulator futtatáskor (: 8080 /) kell lennie egy függő Entitás az ACS-névtérben. Ezután hozzon létre a szabályokat, amelyek ACS használatával feldolgozhatja a jogcímeket a függő Entitás számára.

## <a name="create-rules"></a>Szabályok létrehozása
Ebben a feladatban határozza meg a szabályokat, amelyek meghatározzák a jogcímek átadott hogyan IP-címekről a függő Entitás. Ez az útmutató céljából azt egyszerűen konfigurálja ACS nélkül történő másolását a bemeneti jogcímek adattípusokat és az értékeket közvetlenül a kimeneti jogkivonatot szűrését, és módosítja azokat.

1. Az ACS felügyeleti portál fő lapján kattintson a **csoportok szabály**.
2. Az a **csoportok** kattintson **Szabálycsoportja alapértelmezett Azure webalkalmazás**.
3. Az a **szabály csoport szerkesztése** kattintson **Generate**.
4. Az a **szabályok létrehozása: alapértelmezett szabály csoport Azure webalkalmazás** lapon, győződjön meg arról, a Windows Live ID be van jelölve, majd kattintson **Generate**.    
5. Az a **szabály csoport szerkesztése** kattintson **mentése**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>A ACS névterét tanúsítvány feltöltése
Ebben a feladatban feltöltése egy. PFX-tanúsítvány, amely aláírásához hozta létre a ACS névterét a jogkivonat-kérelmeket.

1. Az ACS felügyeleti portál fő lapján kattintson a **tanúsítványok és kulcsok**.
2. Az a **tanúsítványok és kulcsok** kattintson **Hozzáadás** fent **jogkivonat-aláíró**.
3. Az a **hozzáadása jogkivonat-aláíró tanúsítvány és kulcs** lap:
   1. A a **használt** területen kattintson **függő alkalmazás** válassza **Azure Web Apps** (amely a korábban állítja be a függő entitás alkalmazás neve).
   2. Az a **típus** szakaszban jelölje be **X.509 tanúsítvány**.
   3. Az a **tanúsítvány** szakaszt, kattintson a Tallózás gombra, és keresse meg a használni kívánt X.509 tanúsítvány fájlt. Ez lesz a. PFX-fájlt. Válassza ki a fájlt, kattintson **nyitott**, és írja be a tanúsítvány jelszavát az a **jelszó** szövegmezőben. Vegye figyelembe, hogy a tesztelési célokra, előfordulhat, hogy önkiszolgáló-signed-tanúsítványt használ. Önaláírt tanúsítvány létrehozásához használja a **új** gombra a **ACS könyvtára** párbeszédpanelen (a későbbiekben olvashat), vagy használja a **encutil.exe** segédprogram a a[projekt webhely] [ project website] Javához készült Azure Starter Kit.
   4. Győződjön meg arról, hogy **elsődleges ellenőrizze** be van jelölve. A **hozzáadása jogkivonat-aláíró tanúsítvány és kulcs** oldal az alábbihoz hasonlóan kell kinéznie.
       ![Jogkivonat-aláíró tanúsítvány hozzáadása][add_token_signing_cert]
   5. Kattintson a **mentése** mentse a beállításokat, és zárja be a **hozzáadása jogkivonat-aláíró tanúsítvány és kulcs** lap.

Ezután tekintse át az adatokat az alkalmazások integrálása lapon, és másolja az URI, szüksége lesz a Java-webalkalmazás ACS használata.

## <a name="review-the-application-integration-page"></a>Olvassa el a alkalmazásintegráció oldalt.
Található összes adatot és a kódot kell konfigurálni a Java-alkalmazásokra (a függő Entitás alkalmazás) működéséhez az ACS használatával az ACS felügyeleti portálra az alkalmazások integrálása oldalán. Szüksége lesz ezeket az információkat a Java-webalkalmazás az összevont hitelesítés konfigurálásakor.

1. Az ACS felügyeleti portál, kattintson a **alkalmazásintegráció**.  
2. Az a **alkalmazásintegráció** kattintson **bejelentkezési oldalak**.
3. Az a **bejelentkezési oldal integrációs** kattintson **Azure Web Apps**.

A a **bejelentkezési oldal integrációs: Azure Web Apps** lapon, az URL-címe a **1. lehetőség: az ACS által szolgáltatott bejelentkezési lapra mutató hivatkozás** a Java-webalkalmazás fogja használni. Ha az Azure Access Control szolgáltatások szűrő könyvtár hozzáadása a Java-alkalmazások kell ezt az értéket.

## <a name="create-a-java-web-application"></a>Java-webalkalmazás létrehozása
1. Belül eclipse-ben a menüben kattintson **fájl**, kattintson a **új**, és kattintson a **dinamikus webes projekt**. (Ha nem lát **dinamikus webes projekt** kattintás után az elérhető projektek tulajdonosaként **fájl**, **új**, majd tegye a következőket: kattintson a **fájl**, kattintson a **új**, kattintson **projekt**, bontsa ki a **webes**, kattintson a **dinamikus webes projekt**, és kattintson a  **Ezután**.) Ebben az oktatóanyagban a nevet a projektnek **MyACSHelloWorld**. (Ellenőrizze a nevet használja, ez az oktatóanyag következő lépései a WAR-fájlt, hogy az elnevezésük MyACSHelloWorld várt). A képernyő jelenik meg a következőhöz hasonló:
   
    ![ACS exampple Hello World projekt létrehozása][create_acs_hello_world]
   
    Kattintson a **Befejezés** gombra.
2. A Eclipse Project Explorer nézet, bontsa ki a **MyACSHelloWorld**. Kattintson a jobb gombbal a **WebContent** (Webes tartalom), majd a **New** (Új) elemre, és végül a **JSP File** (JSP-fájl) elemre.
3. Az a **új JSP-fájl** párbeszédpanelen, a fájl neve **index.jsp**. Tartsa a szülőmappa MyACSHelloWorld/WebContent, mint ahogy az a következő:
   
    ![ACS például JSP-fájl hozzáadása][add_jsp_file_acs]
   
    Kattintson a **Tovább** gombra.
4. Az a **JSP-sablon kiválasztása** párbeszédpanelen válassza **új JSP-fájl (html)** kattintson **Befejezés**.
5. Ha megnyílt az index.jsp fájl az eclipse-ben, adja hozzá a megjelenítendő szöveg **Hello ACS World!** a már meglévő `<body>` elemhez. A frissített `<body>` tartalma megjelenjen-e az alábbiak szerint:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Mentse az index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Az ACS-szűrő könyvtár hozzáadása az alkalmazáshoz
1. A Project Explorer Eclipse meg, kattintson a jobb gombbal **MyACSHelloWorld**, kattintson **Build elérési**, és kattintson a **Build elérési konfigurálása**.
2. Az a **Java Build elérési** párbeszédpanel, kattintson a **szalagtárak** fülre.
3. Kattintson a **könyvtár hozzáadása**.
4. Kattintson a **Azure hozzáférést vezérlő Services szűrés (MS nyitott műszaki)** majd **következő**. A **Azure Access Control szolgáltatások szűrő** párbeszédpanel jelenik meg.  (A **hely** mező lehet egy másik elérési utat, attól függően, hogy a telepítési eclipse-ben, és lehet, hogy a verziószám különböző, attól függően, hogy a szoftverfrissítések.)
   
    ![Az ACS-szűrő könyvtár hozzáadása][add_acs_filter_lib]
5. Egy böngészővel nyitva a **bejelentkezési oldal integrációs** oldalán a kezelési portálon, a Másolás az URL-cím szerepel a **1. lehetőség: az ACS által szolgáltatott bejelentkezési lapra mutató hivatkozás** mezőben, majd illessze be azt a **ACS Hitelesítési végpont** mezőjét az Eclipse-párbeszédpanel.
6. Egy böngészővel nyitva a **függő entitás alkalmazás szerkesztése** oldalán a kezelési portálon, a Másolás az URL-cím szerepel a **tartomány** mezőben, majd illessze be azt a **függő entitás Realm**mezőjét az Eclipse-párbeszédpanel.
7. Belül a **biztonsági** az Eclipse-párbeszédpanel, ha azt szeretné, hogy egy meglévő tanúsítványt, kattintson a szakasz **Tallózás**, keresse meg a tanúsítványt szeretné használni, válassza ki azt, és kattintson **megnyitása**. Ha hozzon létre egy új tanúsítványt szeretne, kattintson a **új** megjelenítéséhez a **új tanúsítvány** párbeszédpanelen adja meg a jelszót, a .cer fájl neve és az új tanúsítvány PFX-fájljának nevét.
8. Ellenőrizze **a tanúsítvány beágyazása a WAR-fájlt**. Az ilyen módon a tanúsítvány beágyazás tartalmazza azt a központi telepítés anélkül, hogy kézzel adjon hozzá egy összetevő. (Ha inkább kell tárolnia a tanúsítványt kívülről a WAR-fájl, sikerült felvenni a tanúsítványt egy szerepkör-összetevő és törölje a jelet **a tanúsítvány beágyazása a WAR-fájlt**.)
9. [Választható] Tartsa **szükséges HTTPS-kapcsolatok** be van jelölve. Állítja ezt a beállítást, ha szüksége lesz a HTTPS protokoll használatával az alkalmazás eléréséhez. Ha nem szeretné, hogy HTTPS-kapcsolatok megkövetelése, törölje ezt a beállítást.
10. A compute emulator üzembe helyezése a **Azure ACS szűrő** beállítások az alábbihoz hasonlóan fog kinézni.
    
    ![A compute emulator üzembe helyezése az Azure ACS szűrő beállításai][add_acs_filter_lib_emulator]
11. Kattintson a **Befejezés** gombra.
12. Kattintson a **Igen** amikor az egy párbeszédpanel, amely arról tájékoztat, hogy egy web.xml fájl jön létre.
13. Kattintson a **OK** bezárásához a **Java Build elérési** párbeszédpanel.

## <a name="deploy-to-the-compute-emulator"></a>A compute emulator telepítése
1. A Project Explorer Eclipse meg, kattintson a jobb gombbal **MyACSHelloWorld**, kattintson a **Azure**, és kattintson a **az Azure-csomag**.
2. A **projektnevet**, típus **MyAzureACSProject** kattintson **következő**.
3. Válassza ki a JDK és az alkalmazáskiszolgálóhoz. (A részletes leírása az alábbi lépéseket a [egy Hello World alkalmazás létrehozása az Azure az eclipse-ben](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) oktatóanyag).
4. Kattintson a **Befejezés** gombra.
5. Kattintson a **Azure emulátorban futtatása** gombra.
6. A Java-webalkalmazás a compute emulator a elindulása után zárja be a böngésző minden példányát (úgy, hogy a jelenlegi böngésző-munkamenetek ne zavarja meg az ACS-bejelentkezés tesztelése).
7. Futtassa az alkalmazást megnyitásával <http://localhost:8080/MyACSHelloWorld/> a böngésző (vagy <https://localhost:8080/MyACSHelloWorld/> Ha bejelölte **szükséges HTTPS-kapcsolatok**). A rendszer kéri a Windows Live ID bejelentkezési azonosítóhoz, akkor meg kell fordítani a függő entitás-alkalmazáshoz megadott visszatérési URL-cím.
8. Amikor befejezte az alkalmazást, kattintson a **visszaállítása Azure Emulátorban** gombra.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban
Telepítse az Azure, lesz szüksége a függő entitás realm módosításához, és visszatérési URL-cím az ACS-névtérhez.

1. Az Azure felügyeleti portálon a a **függő entitás alkalmazás szerkesztése** lapján módosíthatja **tartomány** kell lennie a telepített hely URL-CÍMÉT. Cserélje le **példa** a az üzembe helyezéshez megadott DNS-névvel.
   
    ![Függő entitás realm üzemi használatra][relying_party_realm_production]
2. Módosítsa **visszatérési URL-** kell lennie az alkalmazás URL-CÍMÉT. Cserélje le **példa** a az üzembe helyezéshez megadott DNS-névvel.
   
    ![Függő entitás visszatérési URL-cím üzemi használatra][relying_party_return_url_production]
3. Kattintson a **mentése** a frissített függő entitás realm mentéséhez, és térjen vissza az URL-cím módosításokat.
4. Tartsa a **bejelentkezési oldal integrációs** lapon nyissa meg a böngészőben, a másoláshoz hamarosan szüksége lesz.
5. A Project Explorer Eclipse meg, kattintson a jobb gombbal **MyACSHelloWorld**, kattintson **Build elérési**, és kattintson a **Build elérési konfigurálása**.
6. Kattintson a **szalagtárak** lapra, majd **Azure Access Control szolgáltatások szűrő**, és kattintson a **szerkesztése**.
7. Egy böngészővel nyitva a **bejelentkezési oldal integrációs** oldalán a kezelési portálon, a Másolás az URL-cím szerepel a **1. lehetőség: az ACS által szolgáltatott bejelentkezési lapra mutató hivatkozás** mezőben, majd illessze be azt a **ACS Hitelesítési végpont** mezőjét az Eclipse-párbeszédpanel.
8. Egy böngészővel nyitva a **függő entitás alkalmazás szerkesztése** oldalán a kezelési portálon, a Másolás az URL-cím szerepel a **tartomány** mezőben, majd illessze be azt a **függő entitás Realm**mezőjét az Eclipse-párbeszédpanel.
9. Belül a **biztonsági** az Eclipse-párbeszédpanel, ha azt szeretné, hogy egy meglévő tanúsítványt, kattintson a szakasz **Tallózás**, keresse meg a tanúsítványt szeretné használni, válassza ki azt, és kattintson **megnyitása**. Ha hozzon létre egy új tanúsítványt szeretne, kattintson a **új** megjelenítéséhez a **új tanúsítvány** párbeszédpanelen adja meg a jelszót, a .cer fájl neve és az új tanúsítvány PFX-fájljának nevét.
10. Tartsa **a tanúsítvány beágyazása a WAR-fájlt** be van jelölve, feltéve, hogy a tanúsítvány beágyazása a WAR-fájlt szeretne.
11. [Választható] Tartsa **szükséges HTTPS-kapcsolatok** be van jelölve. Állítja ezt a beállítást, ha szüksége lesz a HTTPS protokoll használatával az alkalmazás eléréséhez. Ha nem szeretné, hogy HTTPS-kapcsolatok megkövetelése, törölje ezt a beállítást.
12. Azure üzembe helyezése Azure ACS szűrési beállítások az alábbihoz hasonlóan fog kinézni.
    
    ![Éles környezet az Azure ACS szűrő beállításai][add_acs_filter_lib_production]
13. Kattintson a **Befejezés** bezárásához a **szerkesztése könyvtár** párbeszédpanel.
14. Kattintson a **OK** bezárásához a **MyACSHelloWorld tulajdonságainak** párbeszédpanel.
15. Az eclipse-ben, kattintson a **Publish Azure felhőbe** gombra. Kövesse a megjelenő utasításokat, hasonló módon végezhető el a **az Azure-bA az alkalmazás közzétételéhez** szakasza a [egy Hello World alkalmazás létrehozása az Azure az eclipse-ben](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) témakör. 

A webes alkalmazás telepítése után zárjon be minden nyitott böngésző-munkamenetek, a webes alkalmazás futtatására és a bejelentkezés Windows Live ID hitelesítő adataival, majd küldi el a függő entitás alkalmazás visszatérési URL-CÍMÉT a rendszer kéri.

Ha végzett használatával az ACS Hello World alkalmazásról, fontos, hogy törölje a központi telepítést (megismerheti a központi telepítés törlése a [egy Hello World alkalmazás létrehozása az Azure az eclipse-ben](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) témakör).

## <a name="next_steps"></a>Következő lépések
Lásd: a vizsgálat, a Security Assertion Markup Language (SAML) az alkalmazáshoz az ACS által visszaadott [SAML megtekintése a Azure Access Control szolgáltatás által visszaadott][How to view SAML returned by the Azure Access Control Service]. További megismerkedhet az ACS azon funkcióit és kifinomultabb forgatókönyvek kísérletezhet, olvassa el [Access Control Service 2.0][Access Control Service 2.0].

Emellett ebben a példában használt a **a tanúsítvány beágyazása a WAR-fájlt** lehetőséget. Ez a beállítás leegyszerűsíti a tanúsítvány telepítéséhez. Ha ehelyett meg szeretné tartani az aláíró tanúsítványban elkülönül a WAR-fájlt, használhatja a következő eljárást:

1. Belül a **biztonsági** szakasza a **Azure Access Control szolgáltatások szűrő** párbeszédpanel, írja be **${env. JAVA_HOME}/MyCert.cer** és törölje a jelet **a tanúsítvány beágyazása a WAR-fájlt**. (Beállítása mycert.cer Ha a tanúsítvány neve eltér.) Kattintson a **Befejezés** bezárhatja a párbeszédpanelt.
2. Másolja a tanúsítványt a központi telepítésben összetevőjeként: bontsa ki a Eclipse Project Explorer **MyAzureACSProject**, kattintson a jobb gombbal **WorkerRole1**, kattintson a **tulajdonságok**, Bontsa ki a **Azure szerepkör**, és kattintson a **összetevők**.
3. Kattintson a **Hozzáadás** parancsra.
4. Belül a **összetevő felvétele** párbeszédpanel:
   
   1. Az a **importálási** szakasz:
      1. Használja a **fájl** gombra kattintva keresse meg a használni kívánt tanúsítványt. 
      2. A **metódus**, jelölje be **másolási**.
   2. A **mint neve**, kattintson a szövegmezőbe, és fogadja el az alapértelmezett nevet.
   3. Az a **telepítés** szakasz:
      1. A **metódus**, jelölje be **másolási**.
      2. A **könyvtárba**, típus **JAVA_HOME %**.
   4. A **összetevő felvétele** párbeszédpanelen a következőhöz hasonlóan kell kinéznie.
      
       ![Tanúsítvány-összetevő hozzáadása][add_cert_component]
   5. Kattintson az **OK** gombra.

Ezen a ponton a tanúsítvány szerepel a központi telepítés. Ne feledje, hogy függetlenül attól, hogy a tanúsítvány beágyazása a WAR-fájlt, vagy összetevőjeként hozzáadása a központi telepítés, a tanúsítvány feltöltése a névtérhez, lásd: a [tanúsítvány feltöltése a ACS névterét] [ Upload a certificate to your ACS namespace] szakasz.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

