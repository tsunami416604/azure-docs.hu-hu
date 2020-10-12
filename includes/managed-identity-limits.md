---
title: fájlbefoglalás
description: fájlbefoglalás
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971016"
---
- Az egyes felügyelt identitások az Azure ad-bérlőben az [Azure ad szolgáltatás korlátai és korlátozásai](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md)című témakörben leírtak szerint számítanak bele az objektum-kvótára.
-   A felügyelt identitások létrehozásának sebessége a következő korlátozásokkal rendelkezhet:

    1. Azure AD-bérlő/Azure-régió: 200 művelet/20 másodperc.
    2. Azure-előfizetések/Azure-régiók: 40 művelet/20 másodperc.

- Amikor felhasználó által hozzárendelt felügyelt identitásokat hoz létre, csak alfanumerikus karakterek (0-9, a-z és A-Z) és a kötőjel (-) támogatottak. Ahhoz, hogy a hozzárendelés egy virtuális géphez vagy virtuálisgép-méretezési csoporthoz megfelelően működjön, a név legfeljebb 24 karakter hosszú lehet.
