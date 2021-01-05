---
title: Felhasználók létrehozása és kezelése
description: Érzékelők és a helyszíni felügyeleti konzol felhasználóinak létrehozása és kezelése. A felhasználók hozzárendelhetők a rendszergazda, a biztonsági elemző vagy a csak olvasási jogosultsággal rendelkező felhasználó szerepköréhez.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c3a9e1c7e96d0392e1f94b71549f612738622dea
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840964"
---
# <a name="about-defender-for-iot-console-users"></a>A IoT-konzol felhasználói

Ez a cikk bemutatja, hogyan hozhatók létre és kezelhetők az érzékelők és a helyszíni felügyeleti konzolok felhasználói. A felhasználói szerepkörök közé tartozik a rendszergazda, a biztonsági elemző vagy a csak olvasási jogosultsággal rendelkező felhasználó. Az egyes szerepkörök az érzékelő vagy a helyszíni felügyeleti konzol eszközeihez tartozó engedélyek tartományához vannak társítva. A szerepkörök úgy vannak kialakítva, hogy megkönnyítsék a IoT készült Azure Defender részletes és biztonságos elérését.

A funkciók nyomon követhetik a felhasználói tevékenységeket, és engedélyezhetik Active Directory bejelentkezést is.

Alapértelmezés szerint a rendszer minden érzékelőt és helyszíni felügyeleti konzolt telepít egy *cyberx és egy támogató* felhasználóval. Ezek a felhasználók speciális eszközöket biztosítanak a hibaelhárításhoz és a beállításhoz. A rendszergazda felhasználóknak be kell jelentkezniük a felhasználói hitelesítő adatokkal, létre kell hozniuk egy rendszergazda felhasználót, majd további felhasználókat kell létrehoznia a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára.

## <a name="role-based-permissions"></a>Szerepköralapú engedélyek
A következő felhasználói szerepkörök érhetők el:

- **Csak olvasás**: a csak olvasási jogosultsággal rendelkező felhasználók olyan feladatokat hajtanak végre, mint például a riasztások és az eszközök megtekintése az eszköz térképén. Ezek a felhasználók hozzáférhetnek a **navigáció** alatt megjelenő beállításokhoz.

- **Biztonsági elemző**: a biztonsági elemzőknek csak olvasási jogosultsággal rendelkező felhasználói engedélyei vannak. Emellett műveleteket végezhetnek az eszközökön, visszaigazolhatja a riasztásokat, és használhatnak vizsgálati eszközöket. Ezek a felhasználók hozzáférhetnek a **Navigálás** és **elemzés** alatt megjelenő beállításokhoz.

- **Rendszergazda**: a rendszergazdák hozzáférhetnek az összes eszközhöz, beleértve a rendszerkonfigurációk definiálását, a felhasználók létrehozását és felügyeletét, valamint egyebeket. Ezek a felhasználók hozzáférhetnek a **Navigálás**, **elemzés** és **felügyelet** alatt megjelenő beállításokhoz.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Szerepköralapú engedélyek a helyszíni felügyeleti konzol eszközeihez

Ez a szakasz a rendszergazdák, a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára elérhető engedélyeket ismerteti a helyszíni felügyeleti konzolon.  

| Engedély | Csak olvasás | Biztonsági elemző | Rendszergazda |
|--|--|--|--|
| A vállalati Térkép megtekintése és szűrése | ✓ | ✓ | ✓ |
| Hely létrehozása |  |  | ✓ |
| Hely kezelése (zónák hozzáadása és szerkesztése) |  |  | ✓ |
| Az eszközök leltárának megtekintése és szűrése | ✓ | ✓ | ✓ |
| Riasztások megtekintése és kezelése: nyugtázás, megismerés és PIN-kód | ✓ | ✓ | ✓ |
| Jelentések készítése |  | ✓ | ✓ |
| Kockázatértékelési jelentések megtekintése |  | ✓ | ✓ |
| Riasztás kizárásának beállítása |  | ✓ | ✓ |
| Hozzáférési csoportok megtekintése vagy meghatározása |  |  | ✓ |
| Rendszerbeállítások kezelése |  |  | ✓ |
| Felhasználók kezelése |  |  | ✓ |
| Riasztási adatküldés a partnereknek |  |  | ✓ |
| Tanúsítványok kezelése |  |  | ✓ |
| Munkamenet időkorlátja, ha a felhasználók nem aktívak | 30 perc | 30 perc  | 30 perc  |

#### <a name="assign-users-to-access-groups"></a>Felhasználók társítása a csoportok eléréséhez

A rendszergazdák növelhetik a felhasználói hozzáférés-vezérlést a Defenderben a IoT, ha a felhasználókat egy adott *hozzáférési csoporthoz* rendelik. A hozzáférési csoportok olyan zónákhoz, helyekhez, régiókhoz és üzleti egységekhez vannak rendelve, amelyekben az érzékelő található. A felhasználók hozzáférési csoportokhoz való hozzárendelésével a rendszergazdák konkrétan vezérelhetik, hogy a felhasználók hogyan kezelhetik és elemezzék az eszközök észlelését. 

Ezzel a módszerrel olyan nagyméretű szervezeteket is használhat, amelyekben a felhasználói engedélyek összetettek vagy globális szervezeti biztonsági házirend alapján meghatározhatók. További információ: a [globális hozzáférés-vezérlés meghatározása](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Szerepköralapú engedélyek az érzékelő eszközökhöz

Ez a szakasz az érzékelő-rendszergazdák, a biztonsági elemzők és a csak olvasási jogosultsággal rendelkező felhasználók számára elérhető engedélyeket ismerteti.  

| Engedély | Csak olvasás | Biztonsági elemző | Rendszergazda |
|--|--|--|--|
| Az irányítópult megtekintése | ✓ | ✓ | ✓ |
| Vezérlő Térkép nagyítási nézetei |  |  | ✓ |
| Riasztások megtekintése | ✓ | ✓ | ✓ |
| Riasztások kezelése: nyugtázás, megismerés és PIN-kód |  | ✓ | ✓ |
| Idősor eseményeinek megtekintése |  | ✓ | ✓ |
| Eszközök, ismert vizsgálati eszközök, programozási eszközök engedélyezése |  | ✓ | ✓ |
| Vizsgálati adatbázis megtekintése | ✓ | ✓ | ✓ |
| Rendszerbeállítások kezelése |  |  | ✓ |
| Felhasználók kezelése |  |  | ✓ |
| DNS-kiszolgálók névkeresési kereséshez |  |  | ✓ |
| Riasztási adatküldés a partnereknek |  | ✓ | ✓ |
| Riasztási megjegyzések létrehozása |  | ✓ | ✓ |
| Programozási változások előzményeinek megtekintése | ✓ | ✓ | ✓ |
| Testreszabott riasztási szabályok létrehozása |  | ✓ | ✓ |
| Egyszerre több értesítés kezelése |  | ✓ | ✓ |
| Tanúsítványok kezelése |  |  | ✓ |
| Munkamenet időkorlátja, ha a felhasználók nem aktívak | 30 perc | 30 perc | 30 perc |

## <a name="define-users"></a>Felhasználók definiálása

Ez a szakasz a felhasználók definiálásának módját ismerteti. A Cyberx, a támogatás és a rendszergazda felhasználók hozzáadhatnak, eltávolíthatnak és frissíthetnek más felhasználói definíciókat.

Felhasználó definiálása:

1. Az érzékelő vagy a helyszíni felügyeleti konzol bal oldali ablaktábláján válassza a **felhasználók** lehetőséget.
2. A **felhasználók** ablakban válassza a **felhasználó létrehozása** lehetőséget.
3. A **felhasználó létrehozása** panelen adja meg a következő paramétereket:

   - **Felhasználónév**: adjon meg egy felhasználónevet.
   - **E-mail**: adja meg a felhasználó e-mail-címét.
   - **Keresztnév: adja** meg a felhasználó utónevét.
   - **Vezetéknév**: adja meg a felhasználó vezetéknevét.
   - **Szerepkör**: adja meg a felhasználó szerepkörét. Lásd: [szerepköralapú engedélyek](#role-based-permissions).
   - **Hozzáférési csoport**: Ha felhasználót hoz létre a helyszíni felügyeleti konzolhoz, adja meg a felhasználó hozzáférési csoportját. Lásd: [globális hozzáférés-vezérlés meghatározása](how-to-define-global-user-access-control.md).
   - **Password (jelszó**): válassza ki a felhasználó típusát a következő módon:
     - **Helyi felhasználó**: adjon meg egy jelszót az érzékelő vagy egy helyszíni felügyeleti konzol felhasználója számára. A jelszónak legalább hat karakterből kell állnia, és tartalmaznia kell betűket és számokat.
     - **Active Directory felhasználó**: engedélyezheti, hogy a felhasználók Active Directory hitelesítő adatok használatával bejelentkezzenek az érzékelőbe vagy a felügyeleti konzolba. A definiált Active Directory csoportok adott jogosultsági szintekhez társíthatók. Konfigurálhat például egy adott Active Directory csoportot, és a csoport összes felhasználóját hozzárendelheti a csak olvasható felhasználói típushoz.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Kezelheti a felhasználókat.":::

## <a name="user-session-timeout"></a>Felhasználói munkamenet időkorlátja

Ha a felhasználók nem aktívak a billentyűzeten vagy az egéren egy adott időpontra, akkor kijelentkezett a munkamenetből, és újra be kell jelentkezniük.

Ha a felhasználók 30 percen belül nem működtek együtt a konzol egerével vagy a billentyűzettel, a munkamenet kijelentkezése kényszerítve lesz.

Ez a funkció alapértelmezés szerint engedélyezve van, és a frissítés után is letiltható. Emellett a munkamenetek számlálási ideje is frissíthető. A munkamenet-idő másodpercben van meghatározva. A definíciók az érzékelő és a helyszíni felügyeleti konzolon alkalmazhatók.

A munkamenet időkorlátja üzenet jelenik meg a konzolon, ha az inaktivitás időtúllépése sikeres volt.

### <a name="control-inactivity-sign-out"></a>Inaktivitás vezérlése – kijelentkezés

A rendszergazda felhasználók engedélyezheti és letilthatja a inaktivitásos kijelentkezést, és módosíthatja az inaktivitási küszöbértékeket.

A parancs elérése:

1. Jelentkezzen be a CLI-be az érzékelő vagy a helyszíni felügyeleti konzolhoz a Defender használatával a IoT rendszergazdai hitelesítő adataival.

2. Írja be a következő szöveget: `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

A funkció letiltásához váltson át a következőre: `infinity_session_expiration = true` `infinity_session_expiration = false` .

A kijelentkezési számlálási időszakok frissítéséhez módosítsa az `= <number>` értéket a szükséges időpontra.


## <a name="track-user-activity"></a>Felhasználói tevékenység nyomon követése 

Az esemény idővonalán nyomon követheti a felhasználói tevékenységeket az egyes érzékelőkön. Az idősor megjeleníti az eseményt vagy az érintett eszközt, valamint azt az időpontot és dátumot, ameddig a felhasználó elvégezte a tevékenységet.

Felhasználói tevékenység megtekintése:

1. Jelentkezzen be az érzékelőbe.
1. Az esemény idővonalán engedélyezze a **felhasználói műveletek** lehetőséget. 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="A felhasználó tevékenységének megtekintése.":::

## <a name="integrate-with-active-directory-servers"></a>Integrálás Active Directory-kiszolgálókkal 

Konfigurálja az érzékelőt vagy a helyszíni felügyeleti konzolt a Active Directory való együttműködéshez. Ez lehetővé teszi Active Directory felhasználók számára, hogy a Active Directory hitelesítő adataik használatával hozzáférhessenek a Defender for IoT-konzolokhoz.

Az LDAP-alapú hitelesítés két típusa támogatott:

- **Teljes hitelesítés**: a rendszer beolvassa a felhasználói adatokat az LDAP-kiszolgálóról. Ilyenek például az utónév, a vezetéknév, az e-mail és a felhasználói engedélyek.

- **Megbízható felhasználó**: a rendszer csak a felhasználói jelszót kéri le. A beolvasott egyéb felhasználói adatok az érzékelőben definiált felhasználókon alapulnak.

### <a name="active-directory-and-defender-for-iot-permissions"></a>A IoT engedélyeinek Active Directory és Defender

Az itt definiált Active Directory-csoportokat társíthatja konkrét jogosultsági szintekhez. Beállíthat például egy adott Active Directory csoportot, és a csoport összes felhasználója számára kijelölheti a RO engedélyeket. További részleteket a [felhasználók létrehozása és kezelése](how-to-create-and-manage-users.md) című témakörben talál.

Active Directory konfigurálása:

1. A bal oldali panelen válassza a **Rendszerbeállítások** elemet.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Megtekintheti Active Directory rendszerbeállításait.":::

2. A **Rendszerbeállítások** panelen válassza a **Active Directory** lehetőséget.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Szerkessze Active Directory konfigurációját.":::

3. Az **Active Directory konfigurációjának szerkesztése** párbeszédpanelen válassza a **Active Directory integráció engedélyezve**  >  **Mentés** lehetőséget. A **Active Directory konfigurációjának szerkesztése** párbeszédpanel kibontja, és megadhatja a Active Directory konfigurálásához szükséges paramétereket.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Adja meg a Active Directory konfigurálásához szükséges paramétereket.":::

    > [!NOTE]
    > - Pontosan úgy kell megadnia az LDAP-paramétereket, ahogy a Active Directoryban megjelennek.
    > - Az összes Active Directory paraméternél csak kisbetűset használjon. A kisbetűset akkor is használja, ha a Active Directory konfigurációi nagybetűket használnak.
    > - Ugyanahhoz a tartományhoz nem állítható be az LDAP és az LDAP. Ugyanakkor a különböző tartományokhoz is használhat egyszerre egy időben.

4. Állítsa be a Active Directory kiszolgáló paramétereit az alábbiak szerint:

   | Kiszolgáló paraméter | Leírás |
   |--|--|
   | Tartományvezérlő teljes tartományneve | Állítsa a teljes tartománynevet (FQDN) pontosan úgy, ahogy az az LDAP-kiszolgálón megjelenik. Adja meg például a következőt: `host1.subdomain.domain.com`. |
   | Tartományvezérlő portja | Adja meg azt a portot, amelyen az LDAP konfigurálva van. |
   | Elsődleges tartomány | Állítsa be a tartománynevet (például: `subdomain.domain.com` ) és a kapcsolódási típust az LDAP-konfigurációnak megfelelően. |
   | Active Directory csoportok | Adja meg az LDAP-kiszolgálón Active Directory konfigurációjában definiált csoportnevet. |
   | Megbízható tartományok | Megbízható tartomány hozzáadásához adja hozzá a tartománynevet és a megbízható tartomány kapcsolódási típusát. <br />A megbízható tartományokat csak a felhasználók által definiált felhasználók számára állíthatja be. |

5. Válassza a **Mentés** lehetőséget.

6. Megbízható kiszolgáló hozzáadásához válassza a **kiszolgáló hozzáadása** lehetőséget, és konfigurálja a másik kiszolgálót.

## <a name="see-also"></a>További információ

[Az érzékelő](how-to-activate-and-set-up-your-sensor.md) 
 aktiválása és beállítása [A helyszíni felügyeleti konzol](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 aktiválása és beállítása [Érzékelő tevékenység nyomon követése](how-to-track-sensor-activity.md)
