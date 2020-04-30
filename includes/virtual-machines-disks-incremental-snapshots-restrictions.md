---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204486"
---
- A növekményes Pillanatképek jelenleg nem helyezhetők át az előfizetések között.
- Jelenleg csak egy adott pillanatkép-családhoz tartozó SAS URI-k hozhatók létre akár öt pillanatképből.
- Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez.
- A lemezenként legfeljebb hét növekményes pillanatkép hozható létre 5 percenként.
- Egyetlen lemezhez összesen 200 növekményes pillanatkép hozható létre.
- A 4 TB-os határon belül a szülő lemez méretének változása előtt és után elvégzett Pillanatképek közötti változások nem olvashatók be. Újra le kell töltenie az átméretezés után létrehozott pillanatkép teljes másolatát. Ezt követően a 4 TB-os határ közötti átméretezés után létrehozott Pillanatképek közötti változásokat is beolvashatja. 
