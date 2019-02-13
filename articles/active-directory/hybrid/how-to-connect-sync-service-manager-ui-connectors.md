---
title: Az Azure AD szinkronizálási szolgáltatáskezelő UI-összekötők |} A Microsoft Docs
description: Ismerje meg az összekötők lap a Synchronization Service Managert, az Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a0d42ff70bfaebf4d544fd81e6767592198ccef
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205596"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Összekötők használata az Azure AD Connect szinkronizálása a Service Manager rendszerrel

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Az összekötők lap a szinkronizálási motor csatlakozik az összes rendszer kezelésére szolgál.

## <a name="connector-actions"></a>Összekötő-műveletek
| Műveletek | Megjegyzés |
| --- | --- |
| Létrehozás |Ne használja. További AD-erdőhöz való csatlakozáshoz, a telepítési varázslóval. |
| Tulajdonságok |Tartomány és szervezeti egységek szűrése használható. |
| [Törlés](#delete) |Vagy törölni szeretné az adatokat az összekötőtérben, illetve törli a kapcsolatot, az adott erdő használják. |
| [Futtatási profilok konfigurálása](#configure-run-profiles) |Kivéve a szűréshez, tartomány konfigurálása itt semmi sem. Ez a művelet segítségével tekintse meg a már konfigurált futtatási profilokat. |
| Futtassa a következőt: |A profil egy egyszeri futtatás indításához használt. |
| Leállítás |Leállítja az összekötő jelenleg fut egy profilt. |
| Összekötő exportálása |Ne használja. |
| Összekötő importálása |Ne használja. |
| Összekötő frissítése |Ne használja. |
| Séma frissítése |Frissíti a gyorsítótárazott séma. Azt részesíti előnyben a telepítési varázsló, a beállítás használata óta, amely szintén frissítések szabályok szinkronizálása. |
| [Keresési Összekötőterét](#search-connector-space) |Objektumok keresése, és kövesse az objektum és a rendszer az adatokat használják. |

### <a name="delete"></a>Törlés
A törlési művelet használható két különböző dolgok.  
![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

A beállítás **törlés csak az összekötő-térben** eltávolítható az összes adat, de a konfiguráció megtartása.

A beállítás **összekötő törlése és összekötő** eltávolítja az adatokat és a konfigurációt. Ha nem szeretne csatlakozni egy erdő már ezt a beállítást használják.

Mindkét lehetőség összes objektum szinkronizálása, és a metaverzum-objektum frissítése. Ez a művelet egy olyan hosszú ideig futó művelet.

### <a name="configure-run-profiles"></a>Futtatási profilok konfigurálása
Ez a beállítás lehetővé teszi, hogy megtekintheti a konfigurált összekötők futtatási profilokat.

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Keresési Összekötőterét
A keresési összekötő terület művelet hasznos keresheti meg az objektumot, és az adatokkal kapcsolatos problémák elhárítása.

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Első lépésként válassza ki a **hatókör**. Kereshet az adatok alapján (RDN, DN-t, rögzítési, részfájának), vagy az objektum (az összes többi beállítást) állapot.  
![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Ha például egy részfájának keresési, egy szervezeti egység összes objektum beolvasása.  
![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
A rácsban válasszon ki egy objektumot, jelölje be **tulajdonságok**, és [, kövesse](tshoot-connect-object-not-syncing.md) a forrás összekötőtérben, a metaverzumba, és a cél összekötőterében.

### <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
Ha módosítja a fiók jelszavát, a szinkronizálási szolgáltatás már nem tudja importálni/exportálni a módosításokat a helyszíni AD.   A következők jelenhetnek meg:

- Az importálási/exportálási lépés az AD-összekötő "no-start – hitelesítő adatok" hibaüzenettel meghiúsul.
- A Windows eseménynaplót az alkalmazás eseménynaplója tartalmaz Event ID 6000, az üzenet hiba "a"contoso.com"kezelőügynök futtatása sikertelen, mert a hitelesítő adatok érvénytelenek voltak."

A probléma megoldásához frissítse az Active Directory tartományi szolgáltatások felhasználói fiókot az alábbiak használatával:


1. Indítsa el a Synchronization Service Managert (KEZDŐ → szinkronizálási szolgáltatás).
</br>![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Nyissa meg a **összekötők** fülre.
3. Válassza ki az AD-összekötő, amely a Tartományi fiók használatára van konfigurálva.
4. A műveletek, válassza ki a **tulajdonságok**.
5. Az előugró párbeszédpanelen válassza ki a csatlakozás az Active Directory-erdő:
6. Az erdő nevét azt jelzi, hogy a megfelelő helyszíni AD.
7. A felhasználó nevét jelzi a szinkronizáláshoz használt AD DS-fiókot.
8. Adja meg az új jelszót az AD DS-fiók a jelszó szövegmező ![az Azure AD Connect szinkronizálási titkosítási kulcs segédprogram](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kattintson az OK gombra az új jelszó mentése, és indítsa újra a szinkronizálási szolgáltatást a régi jelszó törlése a memória-gyorsítótárból.



## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
