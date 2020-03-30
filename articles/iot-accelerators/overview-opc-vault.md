---
title: Mi az OPC Vault - Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az OPC Vault. Konfigurálhatja, regisztrálhatja és kezelheti az OPC UA-alkalmazások tanúsítványéletciklusát a felhőben.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826203"
---
# <a name="what-is-opc-vault"></a>Mi az OPC Vault?

Az OPC Vault egy mikroszolgáltatás, amely konfigurálhatja, regisztrálhatja és kezelheti az OPC UA-kiszolgáló és a felhőben lévő ügyfélalkalmazások tanúsítványéletciklusát. Ez a cikk ismerteti az OPC Vault egyszerű használati esetek.

## <a name="certificate-management"></a>Tanúsítványkezelés

Egy gyártó vállalatnak például csatlakoztatnia kell az OPC UA kiszolgálógépét az újonnan létrehozott ügyfélalkalmazáshoz. Amikor a gyártó a kiszolgálószámítógép kezdeti elérését teszi le, egy hibaüzenet jelenik meg azonnal az OPC UA kiszolgálóalkalmazásán, amely jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus az OPC UA szervergépbe van építve, hogy megakadályozza az engedély nélküli alkalmazáshozzáférést, ami megakadályozza az ördögi hackelést az üzemben.

## <a name="application-security-management"></a>Alkalmazásbiztonsági kezelés
A biztonsági szakember az OPC Vault mikroszolgáltatás segítségével egyszerűen engedélyezheti az OPC UA-kiszolgálószámára a kommunikációt bármely ügyfélalkalmazással, mivel az OPC Vault rendelkezik a tanúsítvány-beállításjegyzék, a tárolás és az életciklus-kezelés összes funkciójával. Most az OPC UA szerver biztonságosan csatlakozik, tud kommunikálni az újonnan épített ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>A teljes OPC Vault architektúra
Az alábbi ábra a teljes OPC Vault architektúrát mutatja be.

![OPC Vault architektúra](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az OPC Vault-kal és annak felhasználásával, itt van a javasolt következő lépés:

> [!div class="nextstepaction"]
> [OPC Vault architektúra](overview-opc-vault-architecture.md)
