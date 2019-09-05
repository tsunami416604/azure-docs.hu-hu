---
title: Az Azure Storage használatának eszközei
description: Az Azure Storage-adatai megtekintését és kezelését lehetővé tevő eszközök listája.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 11838a50d70d1b9a0216505e9ef0958d3b18bdac
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035267"
---
# <a name="azure-storage-client-tools"></a>Azure Storage-ügyféleszközök
Az Azure Storage-felhasználók gyakran szeretnék megtekinteni/használni az adataikat az Azure Storage-ügyfélprogram használatával. Az alábbi táblázatokban számos eszközt listázunk, amelyek lehetővé teszik ezt. Minden blokkban "X"-et helyezünk el, ha az adatabsztrakció enumerálását és/vagy elérését teszi lehetővé. A táblázat azt is megjeleníti, hogy az eszközök ingyenesek-e. A "próbaverzió" kifejezés azt jelzi, hogy az ingyenes próbaverzió, de a teljes termék nem ingyenes. Az "i/N" érték azt jelzi, hogy egy verzió ingyenesen elérhető, míg a vásárláshoz egy másik verzió érhető el.

Csak pillanatképet adott meg az elérhető Azure Storage-ügyféleszközök számára. Ezek az eszközök továbbra is fejlődnek és növekednek a funkcionalitásban. Ha vannak javítások vagy frissítések, küldjön egy megjegyzést, hogy tudassa velünk. Ugyanez igaz, ha ismeri az itt található eszközöket – örömmel vesszük őket.

**Ügyféleszközök Microsoft Azure Storage**

<table>
  <tr>
    <th rowspan="2">Azure Storage-ügyfél eszköz</th>
    <th rowspan="2">Blokkblob</th>
    <th rowspan="2">Lapblob</th>
    <th rowspan="2">Blob hozzáfűzése</th>
    <th rowspan="2">Táblák</th>
    <th rowspan="2">Várólisták</th>
    <th rowspan="2">Fájlok</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure portál</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>I</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>I</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Server Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>I</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Külső gyártótól származó Azure Storage-ügyféleszközök**

Nem ellenőrizte, hogy a következő harmadik féltől származó eszközök milyen funkcionalitást vagy minőséget igényelnek, és azok listája nem a Microsoft jóváhagyását jelenti.

<table>
  <tr>
    <th rowspan="2">Azure Storage-ügyfél eszköz</th>
    <th rowspan="2">Blokkblob</th>
    <th rowspan="2">Lapblob</th>
    <th rowspan="2">Blob hozzáfűzése</th>
    <th rowspan="2">Táblák</th>
    <th rowspan="2">Várólisták</th>
    <th rowspan="2">Fájlok</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Próba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>I</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">Törpemálna Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>I/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Felhőbeli egyesítés</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Próba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>I</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Próba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
