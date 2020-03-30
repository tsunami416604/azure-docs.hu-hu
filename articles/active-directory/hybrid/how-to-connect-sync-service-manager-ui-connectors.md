---
title: Összekötők az Azure AD szinkronizálási szolgáltatáskezelő felhasználói felületén | Microsoft Dokumentumok"
description: Ismerje meg az Összekötők lapot az Azure AD Connect szinkronizálási szolgáltatáskezelőjében.
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
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261046"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Összekötők használata az Azure AD Connect Sync Service Manager rel

![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Az Összekötők lap az összes olyan rendszer kezelésére szolgál, amelyhez a szinkronizálási motor csatlakozik.

## <a name="connector-actions"></a>Összekötő-műveletek
| Műveletek | Megjegyzés |
| --- | --- |
| Létrehozás |Ne használja. További AD-erdőkhöz való csatlakozáshoz használja a telepítővarázslót. |
| Tulajdonságok |Tartomány- és szervezetiegység-szűréshez használatos. |
| [Szabályzat](#delete) |Az összekötő térben lévő adatok törlésére vagy az erdővel való kapcsolat törlésére szolgál. |
| [Futtatási profilok konfigurálása](#configure-run-profiles) |Kivéve a domain szűrés, nincs mit beállítani itt. Ezzel a művelettel megtekintheti a már konfigurált futtatási profilokat. |
| Futtassa a következőt: |Egy profil egyszeri futtatásának indításához használható. |
| Leállítás |Leállítja a profilt jelenleg futtató összekötőt. |
| Összekötő exportálása |Ne használja. |
| Összekötő importálása |Ne használja. |
| Összekötő frissítése |Ne használja. |
| Séma frissítése |Frissíti a gyorsítótárazott sémát. A telepítővarázslóban ajánlott a beállítás használata, mivel ez a szinkronizálási szabályokat is frissíti. |
| [Összekötő tér keresése](#search-connector-space) |Objektumok keresésére, objektumok és adatainak követésére szolgál a rendszeren keresztül. |

### <a name="delete"></a>Törlés
A törlési művelet két különböző dologra szolgál.  
![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Az **összekötőterület törlése** lehetőség csak az összes adatot eltávolítja, de megtartja a konfigurációt.

Az **Összekötő és az összekötőtér törlése** lehetőség eltávolítja az adatokat és a konfigurációt. Ez a beállítás akkor használatos, ha már nem kíván erdőhöz csatlakozni.

Mindkét beállítás szinkronizálja az összes objektumot, és frissíti a metaverzum objektumokat. Ez a művelet egy hosszú ideig futó művelet.

### <a name="configure-run-profiles"></a>Futtatási profilok konfigurálása
Ez a beállítás lehetővé teszi az összekötőhöz konfigurált futtatási profilok megtekintését.

![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Összekötő tér keresése
A keresési összekötő terület művelet hasznos az objektumok kereséséhez és az adatproblémák elhárításához.

![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Először jelöljön ki egy **hatókört.** Kereshet az adatok (RDN, DN, Anchor, Sub-Tree) vagy az objektum állapota (minden egyéb beállítás) alapján.  
![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Ha például alfa-keresést végez, akkor egy szervezeti egység összes objektumát megkapja.  
![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Ebből a rácsból kijelölhet egy objektumot, kiválaszthatja a **tulajdonságokat**, és [követheti azt](tshoot-connect-object-not-syncing.md) a forrásösszekötő térből, a metaverzumon keresztül és a célösszekötő térszámára.

### <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
Ha módosítja a fiók jelszavát, a szinkronizálási szolgáltatás a továbbiakban nem tudja importálni/exportálni a módosításokat a helyszíni AD-re.   A következők jelenhetnek meg:

- Az AD-összekötő importálási/exportálási lépése "nincs indítási hitelesítő adat" hiba miatt sikertelen.
- A Windows Eseménynapló csoportban az alkalmazás eseménynaplója a 6000-es azonosítójú hibát tartalmazza, és a következő üzenetet tartalmazza: "A "contoso.com" felügyeleti ügynök nem futtatható, mert a hitelesítő adatok érvénytelenek voltak."

A probléma megoldásához frissítse az AD DS felhasználói fiókot az alábbi módon:


1. Indítsa el a Szinkronizálási szolgáltatáskezelőt (START → Szinkronizálási szolgáltatás).
</br>![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Nyissa meg az **Összekötők** lapot.
3. Válassza ki azt az AD-összekötőt, amely az AD DS-fiók használatára van konfigurálva.
4. A Műveletek csoportban válassza a **Tulajdonságok lehetőséget.**
5. Az előugró párbeszédpanelen válassza a Csatlakozás az Active Directory erdőhöz lehetőséget:
6. Az erdő neve a megfelelő helyszíni AD-t jelzi.
7. A felhasználónév a szinkronizáláshoz használt AD DS-fiókot jelöli.
8. Adja meg az AD DS-fiók új ![jelszavát az Azure AD Connect Sync Encryption Key Utility jelszó mezőjében.](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kattintson az OK gombra az új jelszó mentéséhez és a Szinkronizálási szolgáltatás újraindításához a régi jelszó memória-gyorsítótárból történő eltávolításához.



## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
