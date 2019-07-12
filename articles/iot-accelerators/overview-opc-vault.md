---
title: Mi az OPC-tároló – Azure |} A Microsoft Docs
description: Az OPC-tároló áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 40a9016ac7a10175b51f0fb6f072dd089bde3a51
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606283"
---
# <a name="what-is-opc-vault"></a>Mi az OPC-tároló?

Az OPC-tároló, konfigurálása, regisztrálja, és az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben a tanúsítvány életciklusának kezelése mikroszolgáltatások. Ez a cikk ismerteti az OPC-tároló egyszerű használati eseteket.

## <a name="certificate-management"></a>Tanúsítványok kezelése

Például egy gyártóvállalat cég az újonnan létrehozott ügyfélalkalmazás saját OPC UA-kiszolgáló gép csatlakozni kell. Ha a gyártó hajt végre a kezdeti hozzáférés a kiszolgáló gép, egy hibaüzenet azonnal jelenik meg a OPC UA kiszolgálói alkalmazás azt jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus jön létre az OPC UA kiszolgáló gép minden nem engedélyezett alkalmazás-hozzáférés, amely megakadályozza az üzemi képzett ismétlem elkerülése érdekében.

## <a name="application-security-management"></a>Alkalmazás biztonsági kezelése
Professzionális biztonsági OPC tároló mikroszolgáltatás használatával könnyen engedélyezheti az OPC UA-kiszolgálóval való kommunikációhoz bármelyik ügyfélalkalmazás, mert OPC-tároló tanúsítvány rendszerleíró adatbázis, tárolására és életciklus-felügyelet a függvényeket. Most már az OPC UA-kiszolgálóval biztonságos csatlakoztatva van, akkor kommunikálhatnak az újonnan létrehozott ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>A teljes OPC tároló-architektúra
A következő ábra szemlélteti a teljes OPC tároló-architektúra.

![Az OPC-tároló-architektúra](media/overview-opc-vault-architecture/opc-vault.png)