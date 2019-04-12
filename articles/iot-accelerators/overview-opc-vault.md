---
title: Mi az OPC-tároló – Azure |} A Microsoft Docs
description: Az OPC-tároló áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99dfcaeb1ef5b52e6827f1b3ac65d6201557a8fb
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490376"
---
# <a name="what-is-opc-vault"></a>Mi az OPC-tároló?

Az OPC-tároló, konfigurálása, regisztrálja, és az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben a tanúsítvány életciklusának kezelése mikroszolgáltatások. Ez a cikk ismerteti az OPC-tároló egyszerű használati eseteket.

## <a name="certificate-management"></a>Tanúsítványkezelés

Például egy gyártóvállalat cég az újonnan létrehozott ügyfélalkalmazás saját OPC UA-kiszolgáló gép csatlakozni kell. Ha a gyártó hajt végre a kezdeti hozzáférés a kiszolgáló gép, egy hibaüzenet azonnal jelenik meg a OPC UA kiszolgálói alkalmazás azt jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus jön létre az OPC UA kiszolgáló gép minden nem engedélyezett alkalmazás-hozzáférés, amely megakadályozza az üzemi képzett ismétlem elkerülése érdekében.

## <a name="application-security-management"></a>Alkalmazás biztonsági kezelése
Professzionális biztonsági OPC tároló mikroszolgáltatás használatával könnyen engedélyezheti az OPC UA-kiszolgálóval való kommunikációhoz bármelyik ügyfélalkalmazás, mert OPC-tároló tanúsítvány rendszerleíró adatbázis, tárolására és életciklus-felügyelet a függvényeket. Most már az OPC UA-kiszolgálóval biztonságos csatlakoztatva van, akkor kommunikálhatnak az újonnan létrehozott ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>A teljes OPC tároló-architektúra
A következő ábra szemlélteti a teljes OPC tároló-architektúra.

![Az OPC-tároló-architektúra](media/overview-opc-vault-architecture/opc-vault.png)