---
title: Mi az Azure IoT OPC UA-tanúsítványkezelés |} A Microsoft Docs
description: Az OPC-tároló áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759289"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Mi az Azure IoT nyílt Platform kommunikáció (OPC) UA tanúsítványkezelés?

Az Azure IoT OPC UA tanúsítványkezelés, is tudja, és az OPC-tárolónak, mikroszolgáltatások, amelyek konfigurálni, regisztrálása, és az OPC UA-kiszolgáló és ügyfél alkalmazásokat a felhőben a tanúsítvány életciklusának kezelése. Ez a cikk ismerteti az OPC-tároló egyszerű használati eseteket.

## <a name="certificate-management"></a>Tanúsítványkezelés

Például egy gyártóvállalat cég az újonnan létrehozott ügyfélalkalmazás saját OPC UA-kiszolgáló gép csatlakozni kell. Ha a gyártó hajt végre a kezdeti hozzáférés a kiszolgáló gép, egy hibaüzenet azonnal jelenik meg a OPC UA kiszolgálói alkalmazás azt jelzi, hogy az ügyfélalkalmazás nem biztonságos. Ez a mechanizmus jön létre az OPC UA kiszolgáló gép minden nem engedélyezett alkalmazás-hozzáférés, amely megakadályozza az üzemi képzett ismétlem elkerülése érdekében.

## <a name="application-security-management"></a>Alkalmazás biztonsági kezelése
Professzionális biztonsági OPC tároló mikroszolgáltatás használatával könnyen engedélyezheti az OPC UA-kiszolgálóval való kommunikációhoz bármelyik ügyfélalkalmazás, mert OPC-tároló tanúsítvány rendszerleíró adatbázis, tárolására és életciklus-felügyelet a függvényeket. Most már az OPC UA-kiszolgálóval biztonságos csatlakoztatva van, akkor kommunikálhatnak az újonnan létrehozott ügyfélalkalmazás

## <a name="the-complete-opc-vault-architecture"></a>A teljes OPC tároló-architektúra
A következő ábra szemlélteti a teljes OPC tároló-architektúra.

![Az OPC-tároló-architektúra](media/overview-opc-vault-architecture/opc-vault.png)