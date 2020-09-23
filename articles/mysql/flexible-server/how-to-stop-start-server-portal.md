---
title: Leállítás/indítás – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet leállítani/elindítani a műveleteket a Azure Database for MySQL az Azure Portalon keresztül.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: b9f406035d32a9af9ba2f5b085bcaca1b51e9d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935047"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL rugalmas kiszolgáló leállítása/elindítása (előzetes verzió)


> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti a rugalmas kiszolgáló leállításának és indításának menetét.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for MySQL rugalmas kiszolgálóval kell rendelkeznie.

## <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a leállítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.

> [!NOTE]
> A kiszolgáló leállítása után a többi felügyeleti művelet nem érhető el a rugalmas kiszolgáló számára.

## <a name="start-a-stopped-server"></a>Leállított kiszolgáló indítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az elindítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.

> [!NOTE]
> A kiszolgáló elindítása után az összes felügyeleti művelet elérhető lesz a rugalmas kiszolgáló számára.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).

