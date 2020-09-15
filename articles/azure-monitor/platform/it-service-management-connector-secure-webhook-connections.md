---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor
description: Ez a cikk bemutatja, hogyan csatlakoztathatók a ITSM-termékek és-szolgáltatások a biztonságos exportálással Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és felügyeletéhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 39f277fffbb9a76a4be4bfa8aaedeaf3479a989f
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068883"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Az Azure és a ITSM-eszközök összekapcsolhatók a biztonságos exportálás használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a kapcsolatot az IT szolgáltatás-kezelő (ITSM) termék vagy szolgáltatás között a biztonságos exportálás használatával.

A biztonságos exportálás a [it-szolgáltatásmenedzsmenti csatoló (ITSMC)](./itsmc-overview.md)frissített verziója. Mindkét verzió lehetővé teszi, hogy munkaelemeket hozzon létre egy ITSM eszközben, amikor Azure Monitor riasztásokat küld. A funkciók mérőszám-, napló-és műveletnapló-riasztásokat tartalmaznak.

A ITSMC a Felhasználónév és a jelszó hitelesítő adatait használja. A biztonságos exportálás erősebb hitelesítéssel rendelkezik, mert Azure Active Directoryt (Azure AD) használ. Az Azure AD a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Segít a felhasználóknak bejelentkezni, és hozzáférni a belső vagy külső erőforrásokhoz. Az Azure AD és a ITSM segítségével azonosíthatja a külső rendszernek elküldett Azure-riasztásokat (az Azure AD-alkalmazás azonosítója alapján).

> [!NOTE]
> Az Azure ITSM-eszközökhöz való összekapcsolásának lehetősége a biztonságos exportálás előzetes verzióban érhető el.

## <a name="secure-export-architecture"></a>Biztonságos exportálási architektúra

A biztonságos exportálási architektúra a következő új képességeket mutatja be:

* **Új műveleti csoport**: a rendszer a ITSM eszközre küld riasztásokat a biztonságos webhook műveleti csoporton keresztül, a ITSMC által használt ITSM-műveleti csoport helyett.
* **Azure ad-hitelesítés**: a hitelesítés a Felhasználónév/jelszó hitelesítő adatai helyett az Azure ad-n keresztül történik.

## <a name="secure-export-data-flow"></a>Az adatforgalom biztonságos exportálása

A biztonságos exportálási adatfolyam lépései a következők:

1. Azure Monitor a biztonságos exportálás használatára konfigurált riasztást küld.
1. A riasztási adattartalmat egy biztonságos webhook-művelet küldi el a ITSM eszközhöz.
1. A ITSM alkalmazás ellenőrzi az Azure AD-t, ha a riasztás engedélyezve van a ITSM eszköz megadására.
1. Ha a riasztás engedélyezve van, az alkalmazás:
   
   1. Létrehoz egy munkaelemet (például egy incidenst) a ITSM eszközben.
   1. A konfigurációs tétel (CI) AZONOSÍTÓjának kötése az ügyfél-felügyeleti adatbázishoz (CMDB).

![Diagram, amely bemutatja, hogyan kommunikál a ITSM eszköz az Azure-ban A D, az Azure-riasztásokkal és egy műveleti csoporttal.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>A BMC Helix-vel való kapcsolatok

A biztonságos exportálás a BMC Helix-t támogatja. Az integráció néhány előnye:

* **Jobb hitelesítés**: az Azure ad biztonságosabb hitelesítést biztosít a ITSMC-ben gyakran előforduló időtúllépések nélkül.
* **A ITSM eszközben megoldott riasztások: a**metrikus riasztások "tüzelt" és "megoldott" állapotokat valósítanak meg. Ha a feltétel teljesül, a riasztás állapota "tüzelt". Ha a feltétel már nem teljesül, a riasztás állapota "feloldva". A ITSMC-ben a riasztások nem oldhatók fel automatikusan. A biztonságos exportálással a megoldott állapot a ITSM eszközre áramlik, így automatikusan frissül.
* **[Gyakori riasztási séma](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)**: a ITSMC a riasztási adatok sémája eltér a riasztás típusa alapján. A biztonságos exportálásnál létezik egy általános séma az összes riasztási típushoz. Ez a közös séma tartalmazza a CI-t az összes riasztási típushoz. Az összes riasztási típus képes lesz a CI és a CMDB kötésére.

Kezdje el használni a ITSM-csatoló eszközt a következő lépésekkel:

1. Az alkalmazás regisztrálása az Azure AD-ben.
2. Hozzon létre egy biztonságos webhook-műveleti csoportot.
3. Konfigurálja a partneri környezetet.

## <a name="register-with-azure-active-directory"></a>Regisztrálás Azure Active Directory

Az alábbi lépéseket követve regisztrálja az alkalmazást az Azure AD-ben:

1. Kövesse az [alkalmazás regisztrálása a Microsoft Identity platformmal](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)című témakör lépéseit.
1. Az Azure AD-ben válassza az **alkalmazás közzététele**lehetőséget.
1. Adja **meg** az **alkalmazás azonosítójának URI**azonosítóját.

   [![Képernyőkép az I D-alkalmazás U R I értékének beállításáról.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
1. Válassza a **Mentés** lehetőséget.

## <a name="create-a-secure-webhook-action-group"></a>Biztonságos webhook-műveleti csoport létrehozása

Miután az alkalmazás regisztrálva lett az Azure AD-ben, létrehozhat munkaelemeket a ITSM eszközön az Azure-riasztások alapján, a biztonságos webhook művelettel a műveleti csoportokban.

A műveleti csoportok moduláris és újrahasznosítható módszert biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és Azure Log Analytics riasztásokkal is használhatja a Azure Portal.
A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

Használja a következő eljárást a BMC Helix-környezetben:

1. Jelentkezzen be az Integration studióba.
1. Keresse meg a **create incidenst az Azure-riasztások** folyamatában.
1. Másolja a webhook URL-címét.
   
   ![Képernyőfelvétel a webhook U R L-ről az Integration Studióban.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Webhook egy művelethez való hozzáadásához kövesse az alábbi utasításokat a biztonságos webhookhoz:

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza a **figyelő**elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.
1. Válassza a **riasztások**  >  **kezelése műveletek**lehetőséget.
1. Válassza a [műveleti csoport hozzáadása](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)lehetőséget, és töltse ki a mezőket.
1. Írjon be egy nevet a **műveleti csoport neve** mezőbe, és adjon meg egy nevet a **rövid név** mezőben. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.
1. Válassza a **biztonságos webhook**lehetőséget.
1. Válassza ki az alábbi adatokat:
   1. Válassza ki a regisztrált Azure Active Directory példány objektumazonosítóát.
   1. Az URI esetében illessze be a BMC Helix-környezetből másolt webhook URL-címét.
   1. Állítsa be **az általános riasztási séma engedélyezése** **beállítást igen**értékre. 

   Az alábbi képen egy minta biztonságos webhook-művelet konfigurációja látható:

   ![A biztonságos webhook műveletet bemutató képernyőkép.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-partner-environment"></a>A partneri környezet konfigurálása

### <a name="connect-bmc-helix-to-azure-monitor"></a>A BMC Helix összekapcsolása Azure Monitor

A következő szakaszokban részletesen ismertetjük a BMC Helix-termék és az Azure-beli biztonságos exportálás összekapcsolását.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* Az Azure AD regisztrálva van.
* A BMC Helix multi-Cloud Service Management támogatott verziója (20,02-es vagy újabb verzió).

### <a name="configure-the-bmc-helix-connection"></a>A BMC Helix-kapcsolatok konfigurálása

1. Kövesse az 20,02-es [verzióra vonatkozó, előre összeépített integráció engedélyezése a Azure Monitorrel](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)című témakör utasításait.

1. A BMC Helix-kapcsolat konfigurációjának részeként nyissa meg az integrációs BMC-példányt, és kövesse az alábbi utasításokat:

   1. Válassza a **katalógus**lehetőséget.
   1. Válassza az **Azure-riasztások**lehetőséget.
   1. Válassza az **Összekötők**lehetőséget.
   1. Válassza a **konfiguráció**lehetőséget.
   1. Jelölje be az **új kapcsolatok** konfigurációjának hozzáadása elemet.
   1. Adja meg a konfigurációs szakasz információit:
      - **Név**: hozza létre a sajátját.
      - **Engedélyezési típus**: **nincs**
      - **Leírás**: hozza létre a sajátját.
      - **Webhely**: **felhő**
      - **Példányok száma**: **2**, az alapértelmezett érték.
      - **Ellenőrzés**: a használat engedélyezéséhez alapértelmezés szerint ki van választva.
      - Az Azure-bérlő azonosítója és az Azure-alkalmazás azonosítója a korábban megadott alkalmazásból származik.

![A BMC-konfigurációt megjelenítő képernyőkép.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Következő lépések

* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
