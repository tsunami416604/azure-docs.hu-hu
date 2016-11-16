---
title: "Az Azure Active Directory hozzáférési problémáinak elhárítása | Microsoft Docs"
description: "Ismerje meg, milyen lépéseket kell megtennie a szervezete online erőforrásaival kapcsolatos hozzáférési problémák megoldásához."
services: active-directory
keywords: "eszközalapú feltételes hozzáférés, eszközregisztráció, eszközregisztráció engedélyezése, eszközregisztráció és MDM"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Az Azure Active Directory hozzáférési problémáinak elhárítása
Szervezete SharePoint Online intranetjéhez próbál hozzáférni, ám „hozzáférés megtagadva” hibaüzenetet kap. Mi a következő lépés?


Ez a cikk szervezete online erőforrásaival kapcsolatos hozzáférési problémák megoldásához kínál segítséget.

Az Azure Active Directory (Azure AD) hozzáférési problémáinak megoldásához olvassa el a cikk megfelelő eszközplatformra vonatkozó szakaszát:

* Windows-eszköz
* iOS-eszköz (Térjen vissza később, ha iPhone-okhoz és iPadekhez nyújtott támogatást keres.)
* Android-eszköz (Térjen vissza később, ha androidos telefonokhoz és táblagépekhez nyújtott támogatást keres.)

## <a name="access-from-a-windows-device"></a>Elérés Windows-eszközről
Amennyiben eszköze az alábbi platformok egyikét futtatja, a következő szakaszokban keresse meg azt a hibaüzenetet, amely akkor jelenik meg, amikor megpróbál hozzáférni az alkalmazáshoz vagy szolgáltatáshoz:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Az eszköz nincs regisztrálva
Ha az eszköze nincs regisztrálva az Azure AD-vel és az alkalmazást eszközalapú házirend védi, az alábbi hibaüzeneteket láthatja:

![„Innen nem érheti el” üzenetek nem regisztrált eszközök esetén](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Ha az eszköze tartomány részeként van csatlakoztatva az Active Directoryhoz a szervezetében, megpróbálhatja a következőt:

1. Győződjön meg róla, hogy a munkahelyi fiókot (Active Directory-fiókját) használva lépett be a Windowsba.
2. Csatlakozzon a vállalati hálózathoz virtuális magánhálózaton (VPN) vagy DirectAccessen keresztül.
3. Miután csatlakozott, zárolja a Windows-munkamenetet a Windows-gomb és az L billentyű lenyomásával.
4. Oldja fel a Windows-munkamenet zárolását a munkahelyi fiókjához tartozó hitelesítő adatok beírásával.
5. Várjon egy percet, majd próbálja meg újból elérni az alkalmazást vagy a szolgáltatást.
6. Ha ugyanazt a lapot látja, kattintson a **További részletek** hivatkozásra, és az ott található információt adja át a rendszergazdának.

Ha az eszköze nincs tartományhoz csatlakoztatva és Windows 10 rendszert futtat, két lehetőség áll rendelkezésre:

* Futtassa az Azure AD Joint
* Adja hozzá a munkahelyi vagy iskolai fiókját a Windowshoz

További információt a két megoldás közötti különbségekről itt talál: [Windows 10-es eszközök használata a munkahelyen ](active-directory-azureadjoin-windows10-devices.md).

Az Azure AD Join futtatásához hajtsa végre az alábbiakat az eszközén futó platformnak megfelelően. (Az Azure AD Join nem érhető el windowsos telefonon.)

**Windows 10 évfordulós frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Kattintson az **Eszköz csatlakoztatása az Azure AD-hez** elemre.
5. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
6. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával.
7. Próbálja meg újból elérni az alkalmazást.

**Windows 10, 2015 novemberi frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Rendszer** > **Névjegy** elemre.
3. Kattintson a **Csatlakozás az Azure AD-hez** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával (Azure AD-fiókjával).
6. Próbálja meg újból elérni az alkalmazást.

Munkahelyi vagy iskolai fiók hozzáadásához tegye a következőket:

**Windows 10 évfordulós frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Próbálja meg újból elérni az alkalmazást.

**Windows 10, 2015 novemberi frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Saját fiókok** elemre.
3. Kattintson a **Munkahelyi vagy iskolai fiók beállítása** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Próbálja meg újból elérni az alkalmazást.

Ha az eszköze nincs tartományhoz csatlakoztatva és Windows 8.1 rendszert futtat, munkahelyi csatlakoztatást hajthat végre, és regisztrálhat a Microsoft Intune-ba a következők elvégzésével:

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Hálózat** > **Munkahely** elemre.
3. Kattintson a **Csatlakozás** parancsra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Kattintson a **Bekapcsolás** elemre.
6. Próbálja meg újból elérni az alkalmazást.

### <a name="browser-is-not-supported"></a>A böngésző nem támogatott
A rendszer megtagadhatja a hozzáférést, ha az alábbi böngészők valamelyikével próbál meg hozzáférni egy alkalmazáshoz vagy szolgáltatáshoz:

* Windows 10 vagy Windows Server 2016 rendszer esetén Chrome, Firefox vagy a Microsoft Edge és a Microsoft Internet Explorer kivételével bármely böngésző
* Firefox Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszer esetén

Az alábbihoz hasonló hibaüzenetet megjelenítő lapot fog látni:

![„Innen nem érheti el” üzenetek nem támogatott böngészők esetén](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

Az egyetlen javítási megoldás egy olyan böngésző használata, amelyet az alkalmazás támogat az adott eszközplatformon.

## <a name="next-steps"></a>Következő lépések
[Azure Active Directory feltételes hozzáférés](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


