---
title: Az Azure Storage használatához szükséges eszközök |} A Microsoft Docs
description: Eszközök, amelyek lehetővé teszik, hogy megtekintése és kezelése az Azure Storage-adatokkal listája.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.component: common
ms.openlocfilehash: 55fa63e75768662a0f909cef0e3cc04b9c2dae41
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526198"
---
# <a name="azure-storage-client-tools"></a>Azure Storage-ügyféleszközök
Az Azure Storage felhasználói gyakran szeretné tudni megtekintése/felhasználhatja azok adatait egy Azure Storage ügyféloldali eszközzel. Az alábbi táblázatokban látható számos olyan eszközt, amely lehetővé teszi ezt. Az "X" elhelyezni a minden egyes Ha, lehetővé teszi a számba venni, illetve az adatok absztrakciós eléréséhez. A táblázatban is látható, ha az eszközök ingyenes-e vagy sem. "Próbaverzió" jelzi, hogy van egy ingyenes próbaverzióra, de a teljes termékkiadásban használata nem ingyenes. "Y/N", az azt jelzi, hogy egy verzió érhető el ingyenes, amíg egy másik verzió elérhető és megvásárolható kiadása.

Csak az elérhető Azure Storage-ügyféleszközök pillanatképét mellékelt. Ezek az eszközök, fejlődnek és a funkciók továbbra is. Ha vannak, javításokat és frissítéseket, írt hozzászólásban tudassa velünk, hogy. Ugyanez érvényes Ha ismeri az eszközöket tartalmazza, amelyek kell lennie a ide - készséggel adja hozzá őket.

**A Microsoft Azure Storage-ügyféleszközök**

<table>
  <tr>
    <th rowspan="2">Az Azure Storage-ügyféleszköz</th>
    <th rowspan="2">Blokkblob</th>
    <th rowspan="2">Lapblob</th>
    <th rowspan="2">Hozzáfűző Blob</th>
    <th rowspan="2">Táblák</th>
    <th rowspan="2">Üzenetsorok</th>
    <th rowspan="2">Fájlok</th>
    <th rowspan="2">Ingyenes</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure-portálon</a></td>
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
    <td><a href="http://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
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
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">A Microsoft Visual Studio Server Explorerben</a></td>
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

**Külső Azure Storage-ügyféleszközök**

A Microsoft nem ellenőrizte a funkció vagy engedte, hogy a következő külső eszközök minőségét, és a listaelem nem jelenti azt, a Microsoft feltüntetése.

<table>
  <tr>
    <th rowspan="2">Az Azure Storage-ügyféleszköz</th>
    <th rowspan="2">Blokkblob</th>
    <th rowspan="2">Lapblob</th>
    <th rowspan="2">Hozzáfűző Blob</th>
    <th rowspan="2">Táblák</th>
    <th rowspan="2">Üzenetsorok</th>
    <th rowspan="2">Fájlok</th>
    <th rowspan="2">Ingyenes</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Az Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Próbaverzió</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate-et: Az Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>I</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">A webalkalmazás az Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>I</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
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
    <td><a href="http://www.gapotchenko.com/cloudcombine">Felhőalapú egyesítése</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Próbaverzió</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
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
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet felhő</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Próbaverzió</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
