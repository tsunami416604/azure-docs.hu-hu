---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230990"
---
A VM-méretek legújabb generációja támogatja a titkosítást a gazdagépen:

|Típus  |Nem támogatott  |Támogatott  |
|---------|---------|---------|
|Általános célú     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Számításra optimalizált     |         | Fsv2        |
|Memóriaoptimalizált     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Tárolásra optimalizált     |         | Ls, Lsv2 (NVMe lemezek nincsenek titkosítva)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (előzetes verzió)        |
|Nagy teljesítményű számítás     | H        | HB, HC, HBv2        |
|Előző generációk     | F, A, D, L, G        | DS, GS, FS, NVv2        |

A virtuális gép méretének frissítése azt eredményezi, hogy az új virtuális gép mérete támogatja a EncryptionAtHost funkciót.
