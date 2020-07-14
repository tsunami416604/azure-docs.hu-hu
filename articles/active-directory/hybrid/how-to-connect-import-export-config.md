---
title: Azure AD Connect konfigurációs beállításainak importálása és exportálása
description: Ez a dokumentum a Felhőbeli üzembe helyezéssel kapcsolatos gyakori kérdéseket ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a13236294f74bbe4bdaf8c1a30408afad09d9796
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225225"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Azure AD Connect konfigurációs beállítások importálása és exportálása (nyilvános előzetes verzió) 

Azure AD Connect üzemelő példányok egyetlen erdőre kiterjedő expressz üzemmódból telepíthetők, és olyan összetett központi telepítések, amelyek egyéni szinkronizálási szabályok használatával szinkronizálhatók több erdőben. A konfigurációs lehetőségek és mechanizmusok nagy száma miatt elengedhetetlen, hogy megértsük, milyen beállítások vannak érvényben, és hogy miként lehet gyorsan üzembe helyezni egy kiszolgálót azonos konfigurációval. Ez a funkció bevezeti az adott szinkronizációs kiszolgáló konfigurációjának katalogizálása és a beállítások új központi telepítésbe való importálásának lehetőségét. A szinkronizálási beállítások különböző pillanatképeit összehasonlítva egyszerűen megjelenítheti a két kiszolgáló közötti különbségeket, illetve az idő múlásával megegyező kiszolgálót. 

Minden alkalommal, amikor a konfiguráció megváltozik a Azure AD Connect varázslóból, a rendszer automatikusan exportálja az új időbélyegzővel ellátott JSON-beállítási fájlt a **%ProgramData%\AADConnect**. A beállítások fájlneve az **alkalmazott-SynchronizationPolicy-*. **Az a JSON, amelyben a fájlnév utolsó része időbélyeg. 

>[!IMPORTANT]
> A rendszer csak a Azure AD Connect által végzett módosításokat exportálja automatikusan. A PowerShell, a Synchronization Service Manager vagy a szinkronizálási szabályok szerkesztője által végrehajtott módosításokat szükség szerint exportálni kell igény szerint a naprakész másolás fenntartásához. Az igény szerinti exportálással egy biztonságos helyen helyezheti el a beállítások másolatát a vész-helyreállítási célokra. 

## <a name="exporting-azure-ad-connect-settings"></a>Azure AD Connect-Beállítások exportálása 

A konfigurációs beállítások összefoglalásának megtekintéséhez nyissa meg az Azure AD Connect eszközt, és válassza ki a megnevezett további feladatot: aktuális konfiguráció megtekintése vagy exportálása. A beállítások gyors összefoglalása megjelenik a kiszolgáló teljes konfigurációjának exportálási lehetőségével együtt. 

Alapértelmezés szerint a rendszer a beállításokat a **%ProgramData%\AADConnect**exportálja, azonban a beállítások egy védett helyre menthetők, hogy vészhelyzet esetén is elérhetők legyenek. A beállításokat a rendszer a JSON-fájlformátum használatával exportálja, és nem lehet kézzel létrehozni vagy szerkeszteni a logikai konzisztencia biztosítása érdekében. A kézzel létrehozott vagy szerkesztett fájl importálása nem támogatott, és nem várt eredményekhez vezethet. 

## <a name="importing-azure-ad-connect-settings"></a>Azure AD Connect beállítások importálása 

A korábban exportált beállítások importálásához tegye a következőket:
 
1. Telepítse a **Azure ad Connectt** egy új kiszolgálóra. 
2. Az **üdvözlőlap** után válassza a **Testreszabás** lehetőséget. 
3. Kattintson a **szinkronizálási beállítások importálása**elemre. Tallózással keresse meg a korábban exportált JSON-beállításokat tartalmazó fájlt.  
4. Kattintson az **Install** (Telepítés) gombra.

![Összetevők telepítése](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Kérjük, felülbírálja a beállításokat ezen a lapon, például a SQL Server használatát a meglévő LocalDB helyett, és nem az alapértelmezett VSA-t, hanem a létező szolgáltatásfiókot használja. Ezeket a beállításokat a rendszer nem importálja a konfigurációs beállítások fájljából, de információkat és összehasonlítási célokat szolgál.

### <a name="import-installation-experience"></a>Telepítési élmény importálása 

A telepítés során az importálási élmény szándékosan egyszerű, minimális bemenetekkel a felhasználótól, így könnyen biztosítható a meglévő kiszolgálók reprodukálhatósága.  

Itt láthatók a telepítési élményben elvégezhető változások. Az összes többi módosítás a Azure AD Connect varázslóból történő telepítés után is elvégezhető.: 
- **Hitelesítő adatok Azure Active Directory**   – az eredeti kiszolgáló konfigurálásához használt Azure globális rendszergazda fiókjának neve alapértelmezés szerint javasolt, és **meg**kell   változtatni, ha egy új könyvtárba szeretné szinkronizálni az adatokat.
- **Felhasználói bejelentkezés**   – az eredeti kiszolgálóhoz konfigurált bejelentkezési beállítások alapértelmezés szerint ki vannak választva, és automatikusan rákérdeznek a hitelesítő adatokra, illetve a konfigurálás során szükséges egyéb információkra is. Ritka esetekben előfordulhat, hogy egy olyan kiszolgálót kell beállítania, amely különböző beállításokkal rendelkezik, hogy elkerülje az aktív kiszolgáló viselkedésének módosítását. Ellenkező esetben kattintson a Tovább gombra, hogy ugyanazokat a beállításokat használja. 
- Helyszíni **címtárbeli hitelesítő adatok**   – a szinkronizálási beállításokban foglalt minden helyszíni címtár esetében meg kell adnia a szinkronizálási fiók létrehozásához szükséges hitelesítő adatokat, vagy egy előre létrehozott egyéni szinkronizálási fiókot kell megadnia. Ez az eljárás megegyezik a tiszta telepítési felülettel, azzal a kivétellel, hogy nem adhat hozzá és nem távolíthat el címtárakat. 
- **Konfigurációs beállítások**   – a tiszta telepítéshez hasonlóan dönthet úgy is, hogy megkezdi a kezdeti beállításokat, hogy az automatikus szinkronizálást vagy az átmeneti üzemmódot engedélyezze. A fő különbség az, hogy az átmeneti üzemmód szándékosan engedélyezve van a konfiguráció és a szinkronizálás eredményének összehasonlításához, mielőtt aktívan exportálja az eredményeket az Azure-ba. 

![Címtárak csatlakoztatása](media/how-to-connect-import-export-config/import2.png)

>[!NOTE]
>Csak egy szinkronizációs kiszolgáló lehet az elsődleges szerepkörben, és aktívan exportálhatja a konfigurációs módosításokat az Azure-ba. Az összes többi kiszolgálót átmeneti módba kell helyezni. 

## <a name="migrating-settings-from-an-existing-server"></a>Beállítások áttelepítése meglévő kiszolgálóról 

Ha egy meglévő kiszolgáló nem támogatja a beállítások kezelését, dönthet úgy, hogy helyben frissíti a kiszolgálót, vagy áttelepíti a beállításokat egy új átmeneti kiszolgálón való használatra.  

Az áttelepítéshez olyan PowerShell-parancsfájlt kell futtatni, amely kibontja a meglévő beállításokat egy új telepítésben való használathoz.Ez a módszer ajánlott a meglévő kiszolgáló beállításainak katalogizálása, majd egy újonnan telepített átmeneti kiszolgálón való alkalmazása.Az eredeti kiszolgáló beállításainak az újonnan létrehozott kiszolgálókhoz való összehasonlítása gyorsan megjeleníti a kiszolgálók közötti változásokat.Ahogy mindig, kövesse a szervezete minősítési folyamatát, és győződjön meg arról, hogy nincs szükség további konfigurálásra.  

### <a name="migration-process"></a>Áttelepítési folyamat 
A beállítások átadásához tegye a következőket:

1. Nyissa meg rendszergazdaként a cmd parancsot az új átmeneti kiszolgálón.
2. **AzureADConnect.msi** kinyeréséhez futtassa a következőt: `msiexec /a msifile/qb TARGETDIR=targetpath` ahol a **msifile** az MSI címe, a **TargetPath** pedig az a könyvtár, ahová ki szeretné bontani a fájlokat.
   
   Például`msiexec /a "C:\Holding\AzureADConnect.msi" TARGETDIR="C:\extractedfiles"`
3. Másolja **MigrateSettings.ps1** a Microsoft Azure ad Connect\Tools könyvtárból a meglévő kiszolgáló egy helyére.  Például: C:\setup.  Ahol a telepítő egy olyan könyvtár, amely a meglévő kiszolgálón lett létrehozva. 
![Címtárak csatlakoztatása](media/how-to-connect-import-export-config/migrate1.png)

4. Futtassa a parancsfájlt az alább látható módon, és mentse a teljes alsó szintű kiszolgáló konfigurációs könyvtárat. Másolja ezt a könyvtárat az új átmeneti kiszolgálóra. Vegye figyelembe, hogy a teljes **exportált ServerConfiguration-*** mappát át kell másolnia az új kiszolgálóra. 
 ![Címtárak csatlakoztatása](media/how-to-connect-import-export-config/migrate2.png)

 ![Címtárak csatlakoztatása](media/how-to-connect-import-export-config/migrate3.png)

5. A **Azure ad Connect** indításához kattintson duplán az ikonra az asztalon. Fogadja el a végfelhasználói licencszerződést, a következő lapon kattintson a **Testreszabás** gombra. 
6. Jelölje be a **szinkronizálási beállítások importálása** jelölőnégyzetet, majd a **Tallózás** gombra kattintva tallózással keresse meg az exportált ServerConfiguration-* mappát, és válassza ki a MigratedPolicy.jsaz áttelepített beállítások importálásához.
 ![Címtárak csatlakoztatása](media/how-to-connect-import-export-config/migrate4.png)

7. Ha az áttelepített beállításokat az alkalmazott beállításokkal szeretné összehasonlítani, keresse meg a legújabb **áttelepített-SynchronizationPolicy-* t. JSON** és **alkalmazott-SynchronizationPolicy-*. A JSON** (* a Time Stamp) a **%ProgramData%\AADConnect**alatt található. A paritás összehasonlításához használja kedvenc fájl-összehasonlító eszközét. 

## <a name="post-installation-verification"></a>Telepítés utáni ellenőrzés 

Az újonnan telepített kiszolgáló exportált beállítási fájljával összehasonlítva az eredetileg importált beállítási fájl, amely a tervezett és a létrejövő üzemelő példány közötti különbségek megismerésének alapvető lépése. A kedvenc párhuzamos szöveges összehasonlító alkalmazása egy azonnali vizualizációt eredményez, amely gyorsan kiemeli a kívánt vagy véletlen változásokat. A korábban manuálisan beállított manuális konfigurációs lépések azonban már nem szükségesek, ezért a szervezet minősítési folyamatát továbbra is biztosítani kell, hogy ne legyen szükség további konfigurálásra. Ez a konfiguráció akkor fordulhat elő, ha speciális beállításokat használ, amelyek jelenleg nem rögzítettek a beállítások kezelése nyilvános előzetes kiadásában. 

Az ismert korlátozások többek között a következők: 
- **Szinkronizálási szabályok**   – az egyéni szabályok elsőbbségének a 0-99 fenntartott tartományba kell esnie, hogy elkerülje a Microsoft szokásos szabályainak ütközését. Ha az egyéni szabályt a fenntartott tartományon kívülre helyezi, az egyéni szabályt a rendszer úgy is elmozdulhat, hogy az általános szabályok bekerülnek a konfigurációba. Hasonló probléma lép fel, ha a konfiguráció módosított szabványos szabályokat tartalmaz. A standard szabályok módosítása határozottan nem megfelelő, és a szabály elhelyezése valószínűleg helytelen. Eszköz visszaírási – ezek a beállítások katalogizálva vannak, azonban a konfiguráció során jelenleg nincsenek alkalmazva. Ha az eszköz visszaírási engedélyezve lett az eredeti kiszolgálón, akkor manuálisan kell konfigurálnia a szolgáltatást az újonnan telepített kiszolgálón. 
- **Szinkronizált objektumtípusok**   – Bár lehetséges a szinkronizált objektumtípusok (felhasználók, névjegyek, csoportok stb.) listájának korlátozása a Synchronization Service Manager használatával, ez a funkció jelenleg nem támogatott a szinkronizálási beállításokon keresztül. A telepítés befejezése után manuálisan kell újraalkalmaznia a speciális konfigurációt. 
- **Egyéni futtatási profilok**   – Bár lehetséges a futtatási profilok alapértelmezett készletének módosítása a Synchronization Service Manager használatával, ez a funkció jelenleg nem támogatott a szinkronizálási beállításokon keresztül. A telepítés befejezése után manuálisan kell újraalkalmaznia a speciális konfigurációt. 
- **A kiépítési hierarchia konfigurálása**   – a Synchronization Service Manager speciális funkciója nem támogatott a szinkronizálási beállításokon keresztül, és a kezdeti telepítés befejezése után manuálisan kell konfigurálni. 
- **AD FS és PingFederate hitelesítés**   – a hitelesítési funkciókhoz társított bejelentkezési módszerek automatikusan ki lesznek választva, azonban interaktív módon meg kell adnia az összes többi szükséges konfigurációs paramétert. 

 ## <a name="next-steps"></a>Következő lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
