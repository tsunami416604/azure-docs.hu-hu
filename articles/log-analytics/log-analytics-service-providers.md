---
title: Elemzési szolgáltatások keresse meg a szolgáltatók |} Microsoft Docs
description: Naplóelemzési segítségével felügyelt szolgáltató (MSPs), a nagyobb vállalatok független szoftver szállítói (ISV-k) és az üzemeltetési szolgáltatók kezelni és megfigyelni a kiszolgálók az ügyfél helyszíni vagy felhőalapú infrastruktúrában.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: meirm
ms.openlocfilehash: 97e36c624e865010ada67f5163af6d7f03de079f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096570"
---
# <a name="log-analytics-features-for-service-providers"></a>Napló Analytics szolgáltatások szolgáltatók
A Naplóelemzési segítségével felügyelt szolgáltató (MSPs), a nagyobb vállalatok, független szoftvergyártók (ISV-k) és üzemeltetési szolgáltatók kezelni és megfigyelni a kiszolgálók az ügyfél helyszíni vagy felhőalapú infrastruktúrában. 

A nagyobb vállalatok megosztása sok a hasonlóság szolgáltatók, különösen, ha egy központi informatikai csoportját, amelyek felügyeletéért felelős informatikai számos különböző üzleti egységek számára. Az egyszerűség, a dokumentum kifejezést használja *szolgáltató* , de ugyanezeket a funkciókat is vállalatok és más ügyfelek számára érhető el.

A partnerek és szolgáltatók, amelyek a a [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analyticshez az elérhető Azure-szolgáltatások egyik [Azure CSP előfizetés](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>A szolgáltatói architektúra

Napló Analytics munkaterületek biztosít módszert a rendszergazda szabályozhatja a folyamat és a naplók elkülönítése, és hozzon létre egy napló architektúra, amely az adott üzleti igényeket elégíti ki a. [Ez a cikk](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) körül munkaterület felügyeleti kapcsolatos általános szempontokról ismerteti. Szolgáltatók további szempontok rendelkezik.

Naplóelemzési munkaterület vonatkozó szolgáltatók három lehetséges architektúrához van:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Elosztottalkalmazás - naplók vannak tárolva az ügyfél-bérlőben található munkaterületek 

Ebben az architektúrában munkaterület van telepítve, amely ahhoz a naplófájlokat, hogy az ügyfél szolgál az ügyfél-bérlőben. A service provider rendszergazdák férhetnek hozzá a munkaterület használatával [Azure Active Directory vendégfelhasználók (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Váltson át az ügyfél könyvtárat fogja tudni elérni a munkaterületek, Azure-portálon a szolgáltatást nyújtó rendszergazdája lesz.

Ez az architektúra előnyei a következők:
* Az ügyfél hozzáférését a naplók segítségével a saját kezelheti [szerepkörön alapuló hozzáférés](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Mindegyik ügyfél rendelkezhet például megőrzési és adatok határértékét saját munkaterület különböző beállításait.
* A felhasználók elkülönítését szabályozási és előírásainak.
* Az egyes munkaterületeken díjat az ügyfél előfizetéséhez be lesz állítva.
* A naplókat a minden típusú erőforrások, nem csupán ügynök-alapú is gyűjti. Például az Azure naplózását.

Ezen architektúra hátrányai a következők:
* A szolgáltató kezeléséhez egyszerre nagyszámú ügyfél bérlők nehezebben.
* Service provider rendszergazdáknak kell építhető ki a könyvtárat.
* A szolgáltató nem adatok elemzése az ügyfelek között.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Közép - naplók munkaterületen található, a service provider bérlő vannak tárolva.

Ebben az architektúrában a naplók nem tárolja az ügyfél bérlők, de csak egy központi helyen belül egy, a szolgáltató előfizetéssel. Az ügyfél virtuális gépeken telepített ügynökök tudják elküldeni a naplókat a munkaterület a munkaterület azonosítója és a titkos kulcs használatával vannak konfigurálva.

Ez az architektúra előnyei a következők:
* Ügyfelek száma nagy kezelése és integrálja ezeket a különböző háttérrendszerek egyszerű.
* A szolgáltató a naplókat, valamint a különböző összetevők, például a funkciók teljes tulajdonosi van, és mentett lekérdezések.
* A szolgáltató végezheti analytics összes ügyfelek.

Ezen architektúra hátrányai a következők:
* Az ügyfelek közötti adatokat nehezen lesz. Ehhez az egyetlen jó módszer, hogy a számítógép tartomány nevét használja.
* Minden ügyfelektől származó összes adat ugyanabban a régióban, egyetlen számlázási és azonos megőrzési és konfigurációs beállítások tárolódnak.
* Azure-hálót és PaaS szolgáltatások, például az Azure Diagnostics és az Azure-naplózás igényel a munkaterületen ugyanazzal a bérlővel erőforrásként így azok nem küldi el a naplókat a központi munkaterületen.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hibrid - naplók munkaterületen található, az ügyfél bérlői tárolódnak, és némelyikük egy központi helyen hívja elő.

A harmadik architektúra vegyes között a két lehetőség közül választhat. A első elosztott architektúra, ahol a helyi minden feldolgozásra alapul, de néhány mechanizmus használatával történő létrehozásához egy központi tárházban naplók. A naplók egy részét a rendszer egy központi helyre a jelentéskészítés és elemzés hívja elő. Ez a rész lehet adattípusok kis számú vagy például napi statisztika tevékenységét.

A központi hely megvalósítását a Naplóelemzési két lehetőség áll rendelkezésre:

1. Központi munkaterület: A szolgáltató egy munkaterület létrehozása a bérlői, és egy parancsfájlt, amelyik a használni a [lekérdezés API](https://dev.loganalytics.io/) rendelkező a [adatok gyűjtemény API](log-analytics-data-collector-api.md) vinnie az adatokat a különböző munkaterületekkel Ez központi hely. Egy másik lehetőség, eltérő parancsfájl [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI, mint egy központi helyen: a Power BI működhet, és a központi hely exportálásakor a különböző munkaterületekkel adatok segítségével Naplóelemzési integrációjával és [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>További lépések
* Létrehozása és konfigurálása munkaterületek használatával automatizálhatja [Resource Manager-sablonok](log-analytics-template-workspace-configuration.md)
* Használatával munkaterületek létrehozásának automatizálása [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Használjon [riasztások](log-analytics-alerts.md) integrálható a meglévő rendszerek
* Összegző jelentéseket használatával [Power bi-ban](log-analytics-powerbi.md)

