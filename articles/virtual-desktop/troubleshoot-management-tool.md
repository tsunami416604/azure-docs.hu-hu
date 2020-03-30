---
title: Windows virtuális asztalkezelő eszköz – Azure
description: A Windows virtuális asztal kezelőeszközével kapcsolatos problémák elhárítása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127487"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>A Windows Virtual Desktop felügyeleti eszköz hibaelhárítása

Ez a cikk a Windows virtuális asztal kezelőeszközének telepítése során fellépő problémákat és azok javítását ismerteti.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Hiba: A felügyeleti eszközszolgáltatások konfigurálva vannak, de az automatikus telepítés sikertelen

Ha sikeresen beállította a felügyeleti eszköz szolgáltatásait, de az automatikus telepítés sikertelen, a következő hibaüzenet jelenik meg:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Ez általában az alábbi két dolog egyikét jelenti:

- A felhasználó rendelkezik tulajdonosi engedélyekkel az előfizetéshez és a globális rendszergazda bérlői szinten, de nem tudnak bejelentkezni az Azure-ba.
- A felhasználó fiókbeállításaiban engedélyezve van a többtényezős hitelesítés.

A probléma megoldásához:

1. Győződjön meg arról, hogy az Azure Active Directory egyszerű felhasználónévhez létrehozott felhasználó rendelkezik a "Közreműködő" előfizetési szinttel.
2. Jelentkezzen be <portal.azure.com> az UPN-fiókkal, és ellenőrizze a többtényezős hitelesítést. Ha be van kapcsolva, kapcsolja ki.
3. Látogasson el a Windows virtuális asztal hozzájárulása lapra, és győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazások is hozzájárultak.
4. Tekintse át a [felügyeleti eszköz telepítése oktatóanyag,](manage-resources-using-ui.md) ha a probléma továbbra is fennáll, és újratelepíteni az eszközt.

## <a name="error-job-with-specified-id-already-exists"></a>Hiba: A megadott azonosítóval rendelkező feladat már létezik

Ha a felhasználó látja a "Feladat a megadott azonosítóval már létezik" hibaüzenetet látja, annak az az oka, hogy a sablon telepítésekor nem adtak meg egyedi nevet az "Alkalmazásnév" paraméterben.

A probléma megoldásához telepítse újra a felügyeleti eszközt az "Alkalmazás név" paraméterrel kitöltve.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Késleltetett hozzájárulási kérdés a felügyeleti eszköz megnyitásakor

A felügyeleti eszköz telepítésekor előfordulhat, hogy a hozzájárulási parancs nem nyílik meg azonnal. Ez azt jelenti, hogy az Azure Web App szolgáltatás betöltése a szokásosnál tovább tart. A kérdés nek meg kell jelennie az Azure Web betöltése után.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>A felhasználó nem telepítheti a felügyeleti eszközt az USA keleti régiójában

Ha egy ügyfél a régiót USA keleti régiójára állítja, nem telepítheti a felügyeleti eszközt.

A probléma megoldásához telepítse a felügyeleti eszközt egy másik régióban. Az eszköz áthelyezése egy másik régióban nem befolyásolja a felhasználói élményt.

## <a name="next-steps"></a>További lépések

- Az eszkalációs sávokról a [Hibaelhárítás áttekintése, visszajelzése és támogatása nyújt be.](troubleshoot-set-up-overview.md)
- Megtudhatja, hogy miként jelentheti a Windows virtuális asztal eszközeivel kapcsolatos problémákat az [ARM Templates for Remote Desktop Services szolgáltatásban.](https://github.com/Azure/RDS-Templates/blob/master/README.md)
- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A felügyeleti eszköz központi telepítéséről a [Felügyeleti eszköz telepítése című](manage-resources-using-ui.md)témakörben olvashat.