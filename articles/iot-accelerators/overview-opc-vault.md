---
title: Mi az OPC-tár – Azure | Microsoft Docs
description: Ez a cikk az OPC-tároló áttekintését tartalmazza. Az OPC UA-alkalmazások számára a felhőben konfigurálhatja, regisztrálhatja és kezelheti a tanúsítványok életciklusát.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826203"
---
# <a name="what-is-opc-vault"></a>Mi az az OPC Vault?

Az OPC-tár egy olyan szolgáltatás, amely a tanúsítványok életciklusát konfigurálhatja, regisztrálhatja és kezelheti a felhőben lévő OPC UA-kiszolgáló és ügyfélalkalmazások számára. Ez a cikk az OPC-tár egyszerű felhasználási eseteit ismerteti.

## <a name="certificate-management"></a>Tanúsítványkezelés

Egy gyártó vállalatnak például csatlakoznia kell az OPC UA-kiszolgáló számítógépéhez az újonnan létrehozott ügyfélalkalmazás számára. Ha a gyártó a kiszolgáló első hozzáférését végzi, az OPC UA-kiszolgáló alkalmazásban egy hibaüzenet jelenik meg, amely jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus az OPC UA-kiszolgáló számítógépén található, hogy megakadályozza a jogosulatlan hozzáférések elérését, ami megakadályozza a gonosz hackelést az üzemi emeleten.

## <a name="application-security-management"></a>Alkalmazások biztonságának kezelése
Egy biztonsági szakember az OPC Vault-szolgáltatást használja az OPC UA-kiszolgáló bármely ügyfélalkalmazás általi kommunikációhoz való egyszerű engedélyezéséhez, mivel az OPC-tároló rendelkezik a tanúsítványok beállításjegyzékének, tárolásának és életciklus-kezelésének összes funkcióját. Az OPC UA-kiszolgáló most már biztonságosan csatlakozik, és képes kommunikálni az újonnan létrehozott ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>Az OPC-tár teljes architektúrája
Az alábbi ábra az OPC-tár architektúráját mutatja be.

![OPC-tár architektúrája](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC-tárolót és annak felhasználási lehetőségeit, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [OPC-tár architektúrája](overview-opc-vault-architecture.md)
