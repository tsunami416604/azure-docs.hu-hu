---
title: Az Azure-beli adatkezelési és-tárolási szolgáltatások védelme Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum olyan Azure Security Center javaslatokat tartalmaz, amelyek segítségével megvédheti adatait és az Azure SQL-szolgáltatást, és a biztonsági szabályzatoknak megfelelően maradhat.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 5f34f5e0c622ff107da880d9a4a77c63568b63b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100942"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Az Azure-beli adatkezelési és-tárolási szolgáltatások védelme Azure Security Center
Ebből a témakörből megtudhatja, hogyan tekintheti meg és implementálhatja az adatkezelési és a tárolási erőforrások biztonsági javaslatait. A Azure Security Center az Azure-erőforrások biztonsági állapotának elemzésekor találta meg ezeket az ajánlásokat.

## <a name="view-your-data-security-information"></a>Az adatbiztonsági adatok megtekintése

1. Az **erőforrás-biztonsági higiénia** szakaszban kattintson az **adatforrások és tárolók erőforrásai**elemre.

   ![Adat& Storage-erőforrások](./media/security-center-monitoring/click-data.png)

    Megnyílik az adatbiztonság lap, amely az adaterőforrásokra vonatkozó ajánlásokat tartalmaz.

     ![Adaterőforrások](./media/security-center-monitoring/sql-overview.png)

Ezen a lapon a következőket teheti:

* Kattintson az **Áttekintés** lapra az összes szervizelni kívánt adatforrásra vonatkozó javaslat. 
* Kattintson az egyes lapokra, és tekintse meg az adott erőforrás típusa szerinti javaslatokat.

    > [!NOTE]
    > A tárolás titkosításával kapcsolatos további információkért lásd: [Az Azure Storage titkosítása](../storage/common/storage-service-encryption.md)inaktív adatokhoz.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Javaslat szervizelése egy adaterőforráson

1. Az összes erőforrás lapon kattintson egy erőforrásra. Megnyílik az információs oldal, amely felsorolja a szervizelni kívánt ajánlásokat.

    ![Erőforrás-információk](./media/security-center-monitoring/sql-recommendations.png)

2. Kattintson egy javaslatra. Megnyílik a javaslat lap, és megjeleníti a **szervizelési lépéseket** a javaslat megvalósításához.

   ![Szervizelési lépések](./media/security-center-monitoring/remediate1.png)

3. Kattintson a **művelet**elvégzése gombra. Megjelenik az erőforrás-beállítások lap.

    ![Javaslat engedélyezése](./media/security-center-monitoring/remediate2.png)

4. Kövesse a **szervizelési lépéseket** , és kattintson a **Mentés**gombra.

## <a name="data-and-storage-recommendations"></a>Az adatkezeléssel és a tárolással kapcsolatos javaslatok

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Tárfiók|20|A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell|A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől.|
|Redis|20|Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve|Csak SSL-kapcsolaton keresztül engedélyezze az Azure cache-t a Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől.|
|SQL|15|Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás|Transzparens adattitkosítás engedélyezése a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében.|
|SQL|15|Az SQL Server naplózását engedélyezni kell|Az Azure SQL Server-kiszolgálók naplózásának engedélyezése. (Csak Azure SQL-szolgáltatás. Nem tartalmazza a virtuális gépeken futó SQL-t.)|
|A "Lake Analytics"|5|A Data Lake Analytics diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|A adatközpont-tároló|5|A Azure Data Lake Store diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|SQL|30|Az SQL-adatbázisok biztonsági réseit szervizelni kell|Az SQL sebezhetőségi felmérés megkeresi az adatbázist a biztonsági rések tekintetében, és az ajánlott eljárásoktól, például a helytelen konfigurációtól, a túlzott engedélyekkel és a nem védett bizalmas adatoktól származó eltéréseket tesz elérhetővé. A talált biztonsági rések feloldása nagyban növelheti az adatbázis biztonsági termetét.|
|SQL|20|Azure AD-rendszergazda kiépítése az SQL Serverhez|Hozzon létre egy Azure AD-rendszergazdát az SQL Server számára az Azure AD-hitelesítés engedélyezéséhez. Az Azure AD-hitelesítés lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.|
|Tárfiók|15|A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell|Naplózza a nem korlátozott hálózati hozzáférést a Storage-fiók tűzfala beállításaiban. Ehelyett konfigurálja úgy a hálózati szabályokat, hogy csak az engedélyezett hálózatokból származó alkalmazások férhessenek hozzá a Storage-fiókhoz. Ha engedélyezni szeretné az adott internetről vagy helyszíni ügyfelekről érkező kapcsolatokat, hozzáférést biztosíthat bizonyos Azure-beli virtuális hálózatokból vagy a nyilvános internetes IP-címtartományok érkező adatforgalomhoz.|
|Tárfiók|1|A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra|Használjon új Azure Resource Manager v2-t a Storage-fiókok számára a következő biztonsági fejlesztések biztosításához: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, erőforrás-kezelő alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, hozzáférés a Key vaulthoz a titkokat és az Azure AD-alapú hitelesítést, valamint a címkék és erőforráscsoportok támogatását a biztonsági felügyelet megkönnyítésére.|

## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő témaköröket:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)

Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő témaköröket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
