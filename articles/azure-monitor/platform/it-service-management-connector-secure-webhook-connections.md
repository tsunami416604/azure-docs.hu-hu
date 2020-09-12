---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a biztonságos exportálással Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568642"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Az Azure és a ITSM-eszközök összekapcsolhatók a biztonságos exportálás használatával

Ez a cikk azt ismerteti, hogyan konfigurálható a ITSM-termék vagy-szolgáltatás közötti kapcsolat a biztonságos exportálás használatával.

A biztonságos exportálás a [ITSMC](./itsmc-overview.md) (IT-szolgáltatás felügyelete) frissített verziója. Mindkét verzió lehetővé teszi, hogy munkaelemeket hozzon létre egy ITSM eszközben, amikor Azure Monitor riasztások tüzet. A funkciók metrikus, napló-és műveletnapló-riasztásokat tartalmaznak.

A [ITSMC](./itsmc-overview.md) felhasználói és jelszavas hitelesítő adatokat használ, míg a biztonságos exportálás erősebb hitelesítést használ, mivel Azure Active Directory (Azure ad). Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Segít a felhasználóknak bejelentkezni, és hozzáférni a belső vagy külső erőforrásokhoz. Az Azure AD és a ITSM segítségével azonosíthatja azokat az Azure-riasztásokat (az Azure AD-alkalmazás AZONOSÍTÓját használva), amelyeket a külső rendszernek küldtek.

> [!NOTE]
> Az Azure és a ITSM eszközök biztonságos exportálást használó eszközei előzetes verzióban érhetők el

## <a name="secure-export-architecture"></a>Biztonságos exportálási architektúra

A biztonságos exportálási architektúra a következő új képességeket mutatja be:

* **Új műveleti csoport** – a rendszer riasztásokat küld a ITSM eszköznek a biztonságos webhook műveleti csoport (a ITSM-művelet helyett a ITSMC-ben) használatával.
* **Azure ad-hitelesítés** – a hitelesítés az Azure ad-t használja felhasználói/jelszó-hitelesítő adatok helyett.

## <a name="secure-export-data-flow"></a>Az adatforgalom biztonságos exportálása

A biztonságos exportálási folyamat lépései a következők:

1) Riasztás, amely biztonságos exportálási tüzek használatára van konfigurálva Azure Monitor
2) A riasztási adattartalmat a biztonságos webhook művelet küldi el a ITSM eszközhöz.
3) A ITSM alkalmazás ellenőrzi az Azure AD-t, ha a riasztás engedélyezve van a ITSM eszköz megadására.
4) Ha a riasztás engedélyezi az alkalmazást:
    1) Létrehoz egy munkaelemet (például incidenst) a ITSM eszközben.
    2) A konfigurációs elemek (CI) AZONOSÍTÓjának kötése az ügyfél-felügyeleti adatbázishoz (CMDB).
![ITSM diagram](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>A BMC Helix-vel való kapcsolatok

A biztonságos exportálás a BMC Helix-t támogatja. Az integráció néhány előnye:

* **Jobb hitelesítés** – az Azure ad biztonságosabb hitelesítést biztosít a ITSMC-ben gyakran előforduló időtúllépések nélkül.
* **A ITSM eszközben megoldott riasztások** – a metrikus riasztások "tüzelt" és "megoldott" állapotot valósítanak meg. Ha a feltétel teljesül, a riasztás állapota "fired". Ha a feltétel már nem teljesül, a riasztás állapota "feloldva". A ITSMC-ben a riasztások nem oldhatók fel automatikusan. A biztonságos exportálással a megoldott állapot a ITSM eszközre áramlik, így automatikusan frissül.
* Az **[általános séma lehetővé teszi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – a ITSMC a riasztási adatok sémája eltér a riasztás típusa alapján. A biztonságos exportálás során általános séma van az összes riasztási típushoz. Ez az új közös séma tartalmazza a CI-t az összes riasztási típushoz. Azzal, hogy minden riasztási típus képes lesz a CI és a CMDB kötésére.

Kezdje el használni a ITSM-csatoló a következő lépésekkel:

1. Az alkalmazás regisztrálása a Azure Active Directory.
2. Hozzon létre egy biztonságos webhook-műveleti csoportot.
3. Konfigurálja a partneri környezetet.

## <a name="register-with-azure-active-directory"></a>Regisztrálás Azure Active Directory

Kövesse az alábbi lépéseket az Azure AD-alkalmazás regisztrálásához Azure Active Directory

1) [Azure AD-létrehozás](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) A Azure Active Directory válassza az "alkalmazás közzététele" lehetőséget.
3) Válassza a beállítás lehetőséget az alkalmazás-azonosító URI-ja [ ![ Azure ad](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox) -ben
4) Kattintson a Save (Mentés) gombra.

## <a name="create-a-secure-webhook-action-group"></a>Biztonságos webhook-műveleti csoport létrehozása

Miután regisztrálta az Azure AD-t, létrehozhat munkaelemeket a ITSM eszközön az Azure-riasztások alapján, a biztonságos webhook művelettel a műveleti csoportokban.
A műveleti csoportok moduláris és újrahasznosítható módszert biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló-riasztásokkal és Azure Log Analytics riasztásokkal is használhatja Azure Portalokban.
A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Kövesse az alábbi eljárást:

BMC Helix-környezetben:

1. Jelentkezzen be az Integration studióba.
2. Keresse meg a Create incidenst az Azure-riasztások folyamatában.
3. Másolja a webhook URL-címét.
![BMC URL-CÍM](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Webhook egy művelethez való hozzáadásához kövesse a biztonságos webhookra vonatkozó utasításokat:

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza a **figyelő**elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.
2. Válassza a **Riasztások**, majd a **Műveletek kezelése** lehetőséget.
3. Válassza a [műveleti csoport hozzáadása](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)lehetőséget, és töltse ki a mezőket.
4. Írjon be egy nevet a **műveleti csoport neve** mezőbe, és adjon meg egy nevet a **rövid név** mezőben. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.
5. **Biztonságos webhook** kiválasztása
6. Válassza a részletek szerkesztése lehetőséget. Az alábbi képen egy példa biztonságos webhook-művelet látható:
    1. Válassza ki a regisztrált Azure Active Directory megfelelő objektum-AZONOSÍTÓját
    2. Illessze be a in URI mezőt a "BMC Helix Environment" fájlból másolt webhook URL-címére.
    3. Állítsa az **általános riasztási sémát** **Igen**értékre. 
7. Az alábbi képen egy minta biztonságos webhook művelet konfigurációja látható: ![ biztonságos webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Partneri környezet konfigurálása

### <a name="connect-bmc-helix-to-azure-monitor"></a>A BMC Helix összekapcsolása Azure Monitor

A következő szakasz részletesen ismerteti, hogyan csatlakoztatható a BMC Helix-termék, és hogyan biztosítható a biztonságos Exportálás az Azure-ban.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az Azure AD regisztrálva van.
* A BMC Helix multi-Cloud Service Management támogatott verziója: 20,02 vagy újabb verzió

A BMC Helix-kapcsolatok konfigurálása:

1) [A 20,2-es verzióra való előre összeépített integráció engedélyezése Azure Monitor](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) A BMC Helix-kapcsolat konfigurációjának részeként nyissa meg az integrációs BMC-példányt, és kövesse az alábbi utasításokat:

1. **Katalógus** kiválasztása
2. **Azure-riasztások** kiválasztása
3. **Összekötők** kiválasztása
4. **Konfiguráció** kiválasztása
5. Válassza az **új kapcsolatok** konfigurációjának hozzáadása lehetőséget
6. Adja meg a konfigurációs szakasz információit.
    1. **Név** – saját létrehozása
    2. **Engedélyezési típus** – none
    3. **Leírás**– saját létrehozása
    4. **Hely**– felhő
    5. **Példányok száma** – 2 – alapértelmezett érték
    6. A jelölőnégyzet alapértelmezés szerint **be** van jelölve, és lehetővé teszi a használatot
    7. Az Azure-bérlő azonosítója, az Azure-alkalmazás azonosítója a "létrehozott Azure Active Directory" lépésben meghatározott alkalmazásból származik.
![BMC-konfiguráció](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Következő lépések

* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
