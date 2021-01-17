---
title: Beszédfelismerési parancssori felület beállításai – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre és kezelhet konfigurációs fájlokat az Azure Speech CLI-vel való használatra.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540057"
---
# <a name="speech-cli-configuration-options"></a>A beszédfelismerési parancssori felület beállításai

A Speech CLI viselkedése a konfigurációs fájlok beállításai alapján is felhasználható, amelyet egy szimbólum használatával lehet megtekinteni `@` . A Speech CLI új beállítást ment egy új `./spx/data` alkönyvtárban, amely a beszédfelismerési parancssori felület aktuális munkakönyvtárában jön létre. Ha konfigurációs értéket keres, a Speech CLI a jelenlegi munkakönyvtárban, majd az adattárolóban, majd más adattárolókban keres `./spx/data` , beleértve a bináris utolsó írásvédett adattárat is `spx` . 

A Speech CLI rövid útmutatójában az adattár használatával mentheti a és az `@key` `@region` értékeket, így nem kell megadnia azokat az egyes `spx` parancsokkal. Ne feledje, hogy a konfigurációs fájlok használatával tárolhatja a saját konfigurációs beállításait, vagy akár az URL-címeket, akár más, futásidőben generált dinamikus tartalmat is megadhat.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Konfigurációs fájlok létrehozása és kezelése az adattárban

Ez a szakasz azt mutatja be, hogyan használható a helyi adattárban található konfigurációs fájl a parancsok beállításainak a használatával történő tárolásához és beolvasásához `spx config` , valamint a beszédfelismerési parancssori felület kimenetének a kapcsolóval való tárolásához `--output` .

A következő példa törli a `@my.defaults` konfigurációs fájlt, hozzáadja a kulcs-érték párokat a fájlban lévő **kulcshoz** és **régióhoz** , és a konfigurációt használja a hívásához `spx recognize` .

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Dinamikus tartalmat is írhat egy konfigurációs fájlba. Az alábbi parancs például létrehoz egy egyéni beszédfelismerési modellt, és egy konfigurációs fájlban tárolja az új modell URL-címét. A következő parancs megvárja, amíg az URL-ben lévő modell használatra készen áll a visszatérés előtt.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

A következő példa két URL-címet ír a `@my.datasets.txt` konfigurációs fájlba. Ebben az esetben hozzáadhat egy `--output` opcionális **hozzáadási** kulcsszót egy konfigurációs fájl létrehozásához, vagy hozzáfűzheti azt a meglévőhöz.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Az adattár-fájlokkal kapcsolatos további részletekért, beleértve az alapértelmezett konfigurációs fájlok ( `@spx.default` , `@default.config` és `@*.default.config` a parancsokra vonatkozó alapértelmezett beállítások) használatát, adja meg a következő parancsot:

```console
spx help advanced setup
```

## <a name="next-steps"></a>További lépések 

* [Batch-műveletek a Speech CLI-vel](./spx-batch-operations.md)