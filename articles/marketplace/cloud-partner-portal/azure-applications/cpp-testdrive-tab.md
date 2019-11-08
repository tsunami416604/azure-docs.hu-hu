---
title: Azure-alkalmazás ajánlata Test Drive | Azure piactér
description: Az Azure Marketplace-en futó tesztelési meghajtó konfigurálása az Azure-beli alkalmazásokhoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826122"
---
# <a name="azure-applications-test-drive-tab"></a>Azure-alkalmazások tesztelési meghajtó lapja

A test Drive (tesztelési meghajtó) lapon megadhatja az ügyfelei számára a próbaverziót.

## <a name="test-drive-benefits"></a>A test Drive előnyei

A próbaverzió létrehozása az ügyfelek számára ajánlott eljárás annak biztosítására, hogy nyugodtan vásárolhatnak. Az elérhető próbaverziós lehetőségek közül a test Drive a leghatékonyabb megoldás, amely kiváló minőségű érdeklődőket generál, és növeli az érdeklődők átalakítását.

Lehetővé teszi az ügyfelek számára, hogy a termék legfontosabb funkcióit és előnyeit gyakorlati, önállóan, valós implementációs forgatókönyvben mutassa be.

## <a name="how-a-test-drive-works"></a>A test Drive működése

A potenciális ügyfelek megkeresik és felfedik az alkalmazást a piactéren. Az ügyfél bejelentkezik, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezetet, hogy rögzített számú órát próbáljon megállapítani, miközben nagy teljesítményű vezetőt kap a követéshez. További információ: [Mi az a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Tesztelési meghajtó beállítása

A következő lépésekkel engedélyezheti és konfigurálhatja a tesztelési meghajtót.

### <a name="to-enable-a-test-drive"></a>Tesztelési meghajtó engedélyezése:

1. Az **új ajánlat**területen válassza a **Test Drive (tesztelési meghajtó** ) fület.
2. A **Test Drive (tesztelési meghajtó**) területen válassza az **Igen** lehetőséget a **tesztvezetés engedélyezéséhez**.

   ![Tesztelési meghajtó engedélyezése](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>A tesztelési meghajtó konfigurálása:

Miután engedélyezte a tesztelési meghajtót, a következő űrlapokat kell kitöltenie a tesztelési meghajtó beállításához:
  
 - Részletek
 - Technikai konfiguráció
 - Tesztelési meghajtó üzembe helyezésének előfizetése – részletek

A következő képernyőfelvételen a test Drive összes űrlapja látható. A mezőhöz hozzáfűzni kívánt csillag (*) azt jelzi, hogy a név megadása kötelező. 

![Tesztelési meghajtó konfigurálása](./media/managed-app-configure-testdrive.png)

A következő táblázat ismerteti a felügyelt alkalmazáshoz tartozó tesztelési meghajtó beállításához szükséges mezőket.  A csillaggal hozzáfűzött mezők megadása kötelező.

|      Mező         |  Leírás      |
|  ---------------   |  ---------------  |
| **Leírás\***  |  Írja le, hogy mit tehet a tesztelési meghajtón. Az alapszintű HTML-címkék használatával formázhatja a leírást. Például &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;Li&gt;, &lt;ol&gt;és fejlécek.                |
| **Felhasználói manuális\***  |  Töltse fel a felhasználói kézikönyvet, amelyet az ügyfelek használhatnak a test Drive-élmény átjárásához. A dokumentumnak. pdf formátumúnak kell lennie.    |
| **A test Drive bemutató videója** |  A tesztvezetés választható videós útmutatója. Az ügyfél megtekintheti ezt a videót, mielőtt elkezdené a tesztelési meghajtót. Adja meg a YouTube vagy a Vimeo Videójának URL-címét. Ha a **+ videó hozzáadása**lehetőséget választja, a rendszer a következő információk megadását kéri:<ul><li>Név</li><li>URL-cím</li><li>Miniatűr (PNG formátumban, 533 x 324 képpont)</li></ul>  |
| **Példányok\***      | Konfigurálhatja, hogy hány példányt szeretne, milyen régió (k), és hogy az ügyfelek milyen gyorsan kapják meg a tesztelési meghajtót. További információkért lásd: [a tesztelési meghajtó közzététele](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Tesztvezetés időtartama (óra)\*** | Adjon meg egy egész számot az órák számaként. Az engedélyezett tartomány 1 és 999 között van. |
| **Test Drive ARM-sablon\***     | Töltsön fel egy tömörített (. zip) fájlt, amely a Azure Resource Manager-sablonjaival rendelkezik az alkalmazáshoz. További információ: [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Hozzáférési információk\***          | Adja meg a hozzáférési adatokat, miután az ügyfél lekéri a tesztet. Például egy URL-cím a tesztvezetés eléréséhez és az adatok aláírásához. . Az alapszintű HTML-címkék használatával formázhatja a leírást. Például &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;Li&gt;, &lt;ol&gt;és fejlécek. |
| **Azure-előfizetési azonosító\***       | Ez hozzáférést biztosít az Azure-szolgáltatásokhoz és a Azure Portalhoz. Az előfizetés az erőforrás-használat jelentését jelenti, és a szolgáltatások számlázása történik. Ha még nem rendelkezik külön Azure-előfizetéssel kizárólag tesztelési meghajtókhoz, hozzon létre egy előfizetést.  |
| **Azure AD-bérlő azonosítója\***          | Adjon meg egy meglévő bérlőt Azure Active Directory, vagy hozzon létre egy bérlőt ehhez a tesztelési meghajtóhoz.  |
| **Azure AD alkalmazás azonosítója\***             | Új alkalmazás létrehozása és regisztrálása. A Microsoft ezt az alkalmazást használja a tesztelési meghajtó példányán végzett műveletek végrehajtásához.  |
| **Azure AD alkalmazás kulcs\***            | Hozzon létre egy hitelesítési kulcsot az alkalmazáshoz, és illessze be ebbe a mezőbe.   |
|  |  |

Miután megadta az összes szükséges információt, válassza a **Save (Mentés** ) lehetőséget a tesztelési meghajtó beállításának befejezéséhez.


## <a name="next-steps"></a>További lépések

[Marketplace lap](./cpp-marketplace-tab.md)
