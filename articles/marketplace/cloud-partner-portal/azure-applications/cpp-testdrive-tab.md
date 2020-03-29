---
title: Azure Alkalmazás ajánlat tesztvezetés | Azure Piactér
description: Hogyan konfigurálhatja a tesztmeghajtót az Azure-alkalmazásajánlathoz az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289005"
---
# <a name="azure-applications-test-drive-tab"></a>Az Azure Applications Tesztmeghajtó lapja

A Tesztvezetés lapon próbaverziót biztosíthatja ügyfeleinek.

## <a name="test-drive-benefits"></a>A Test Drive előnyei

A próbaverzió létrehozása az ügyfelek számára a legjobb gyakorlat annak biztosítására, hogy magabiztosan vásárolhassanak. A rendelkezésre álló próbalehetőségek közül a Test Drive a leghatékonyabb a kiváló minőségű érdeklődők létrehozásában és az érdeklődők fokozott átalakításában.

Gyakorlati, saját irányítású próbaverziót biztosít ügyfeleinek a termék legfontosabb funkcióiról és előnyeiről, amelyet valós megvalósítási forgatókönyv mutat be.

## <a name="how-a-test-drive-works"></a>A tesztvezetés működése

Egy potenciális ügyfél megkeresi és felderíti az alkalmazást a Marketplace-en. Az ügyfél bejelentkezik, és elfogadja a felhasználási feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezetet, hogy megpróbálja a meghatározott számú órát, miközben ön kap egy magasan képzett érdeklődőt, hogy kövesse nyomon. További információ: [Mi az a Tesztvezetés?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Tesztvezetés beállítása

A tesztmeghajtó engedélyezéséhez és konfigurálásához kövesse az alábbi lépéseket.

### <a name="to-enable-a-test-drive"></a>Tesztvezetés engedélyezése:

1. Az **Új ajánlat csoportban**válassza a **Tesztautó** lapot.
2. A **Tesztvezetés**csoportban válassza az **Igen** lehetőséget a **Tesztvezetés engedélyezése mezőben.**

   ![Tesztvezetés engedélyezése](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Tesztmeghajtó konfigurálása:

Miután engedélyezte a tesztvezetést, a tesztvezetés beállításához az alábbi űrlapokat kell kitöltenie:
  
 - Részletek
 - Műszaki konfiguráció
 - A Test Drive-alapú üzembe helyezési előfizetés részletei

A következő képernyőfelvételen látható az összes Tesztmeghajtó űrlap. A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá. 

![Tesztmeghajtó konfigurálása](./media/managed-app-configure-testdrive.png)

Az alábbi táblázat a felügyelt alkalmazás tesztmeghajtójának beállításához szükséges mezőket ismerteti.  A csillaggal hozzáfűzött mezők szükségesek.

|      Mező         |  Leírás      |
|  ---------------   |  ---------------  |
| **Leírás\***  |  Írja le, mit lehet tenni a tesztvezetésen. A leírás formázásához használjon egyszerű HTML-címkéket. Például &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt; &lt;ul, li , ol&gt;és címsorok.                |
| **Használati utasítás\***  |  Töltsön fel egy felhasználói kézikönyvet, amelyet az ügyfelek a Test Drive-élmény átjárásához használhatnak. Ennek a dokumentumnak .pdf fájlnak kell lennie.    |
| **Tesztvezetés bemutató videó** |  A tesztvezetés opcionális videoáttekintése. Az ügyfél megnézheti ezt a videót, mielőtt tesztvezetést hajtana. Adja meg a videó URL-címét a YouTube-on vagy a Vimeo-n. Ha a **+ Videó hozzáadása**lehetőséget választja, a rendszer a következő adatok megadását kéri:<ul><li>Név</li><li>URL-cím</li><li>Miniatűr (PNG formátumban, 533 x 324 képpont)</li></ul>  |
| **példányszám\***      | Állítsa be, hogy hány példányt szeretne, milyen régióban(ok)on, és milyen gyorsan az ügyfelek kaphatják meg a Tesztmeghajtót. További információ: [Test Drive közzététele.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)           |
| **A tesztvezetés időtartama (óra)\*** | Adjon meg egy egész számot az órák számához. A megengedett tartomány 1 és 999 között van. |
| **Test Drive ARM sablon\***     | Töltsön fel egy tömörített (.zip) fájlt, amely rendelkezik az alkalmazáshoz az Azure Resource Manager-sablonokkal. További információ: [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Hozzáférési adatok\***          | Adja meg a hozzáférési információkat, miután az ügyfél megkapja a Tesztmeghajtót. Például egy URL-t a tesztvezetés eléréséhez, és írja alá az adatokat. . A leírás formázásához használjon egyszerű HTML-címkéket. Például &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt; &lt;ul, li , ol&gt;és címsorok. |
| **Azure-előfizetés azonosítója\***       | Ez hozzáférést biztosít az Azure-szolgáltatásokhoz és az Azure Portalhoz. Az előfizetés, ahol az erőforrás-használat jelentik, és a szolgáltatások számlázása. Ha még nem rendelkezik külön Azure-előfizetéssel csak tesztmeghajtókhoz, hozzon létre egy előfizetést.  |
| **Azure AD-bérlőazonosító\***          | Adjon meg egy meglévő bérlőt az Azure Active Directoryban, vagy hozzon létre egy bérlőt ehhez a tesztmeghajtóhoz.  |
| **Az Azure AD-alkalmazás azonosítója\***             | Hozzon létre és regisztráljon egy új alkalmazást. A Microsoft ezt az alkalmazást használja a Test Drive-példányon végzett műveletek végrehajtására.  |
| **Azure AD alkalmazáskulcs\***            | Hozzon létre egy hitelesítési kulcsot az alkalmazáshoz, és illessze be ebbe a mezőbe.   |
|  |  |

Miután megadt minden szükséges információt, a **Mentés gombra** a tesztvezetés beállításának befejezéséhez válassza a Mentés gombot.


## <a name="next-steps"></a>További lépések

[Marketplace lap](./cpp-marketplace-tab.md)
