---
title: Példák a Azure Lab Services-osztályokra | Microsoft Docs
description: Bizonyos típusú osztályokat biztosít, amelyekhez a Azure Lab Services használatával állíthatja be a laborokat.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: d24b22ad7f306e6334369ec859f5f01b2d32184e
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695833"
---
# <a name="example-class-types-on-azure-lab-services"></a>Példa Azure Lab Services
Ez a cikk a Azure Lab Services-on beállítható példaként szolgáló osztályok listáját tartalmazza.

## <a name="deep-learning-in-natural-language-processing"></a>Mélyreható tanulás a természetes nyelvi feldolgozásban
Beállíthat egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) formája, amely lehetővé teszi, hogy a számítógépek fordítással, beszédfelismeréssel és más nyelvi felismerési képességekkel rendelkeznek. Az NLP osztályba tartozó diákok linuxos virtuális gépet (VM) kapnak, amelyből megtudhatja, hogyan alkalmazhat neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mély tanulási modellek fejlesztéséhez. 

Az ilyen típusú laborok beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a természetes nyelvi feldolgozás terén a Azure Lab Services használatával](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Rendszerhéj-parancsfájlok Linux rendszeren
Beállíthat egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához. A parancsfájlok hasznos részét képezik a rendszerfelügyeletnek, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a példában az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat tartalmazza. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek a bash-parancsokat és a Ruby-t ötvözik. Ez a módszer lehetővé teszi a Ruby számára, hogy a Shell használatával kommunikáljon a környékre és bash-parancsokra. 

Az ilyen programozási osztályokat tartalmazó tanulók Linux rendszerű virtuális gépeket szereznek be a Linux alapjaihoz, és megismerhetik a bash rendszerhéj parancsfájljait is. A linuxos virtuális gépen engedélyezve van a távoli asztali hozzáférés, és telepítve van a [gedit](https://help.gnome.org/users/gedit/stable/) és a [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztő.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkért lásd: [rendszerhéj-parancsfájlok futtatása Linuxon](class-type-shell-scripting-linux.md).

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet létrehozása a természetes nyelvi feldolgozással kapcsolatos mély tanuláshoz Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Rendszerhéj-parancsfájlok Linux rendszeren](class-type-shell-scripting-linux.md)