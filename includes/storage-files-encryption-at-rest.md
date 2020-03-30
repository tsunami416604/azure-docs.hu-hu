---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597815"
---
Az Azure Files-ban tárolt összes adat inaktív módon titkosítva van az Azure storage-szolgáltatás titkosításával (SSE). A tárolási szolgáltatás titkosítása a Windows BitLocker szolgáltatásához hasonlóan működik: az adatok a fájlrendszer szintje alatt titkosítva vannak. Mivel az adatok titkosítva vannak az Azure fájlmegosztás fájlrendszere alatt, mivel lemezre vannak kódolva, nem kell hozzáférnie az ügyfél alapjául szolgáló kulcshoz az Azure fájlmegosztás olvasásához vagy írásához.

Alapértelmezés szerint az Azure Files-ban tárolt adatok microsoftáltal kezelt kulcsokkal vannak titkosítva. A Microsoft által felügyelt kulcsokkal a Microsoft rendelkezik az adatok titkosításához/visszafejtéséhez szükséges kulcsokhoz, és rendszeresen elforgatja azokat. Azt is választhatja, hogy kezelje a saját kulcsokat, amely lehetővé teszi, hogy ellenőrizzék a forgatási folyamatot. Ha úgy dönt, hogy titkosítja a fájlmegosztások ügyfél által kezelt kulcsok, az Azure Files jogosult a kulcsok eléréséhez az ügyfelek olvasási és írási kérések teljesítéséhez. Az ügyfél által felügyelt kulcsok bármikor visszavonhatja ezt az engedélyt, de ez azt jelenti, hogy az Azure-fájlmegosztás már nem lesz elérhető az SMB-n vagy a FileREST API-n keresztül.

Az Azure Files ugyanazt a titkosítási sémát használja, mint a többi Azure storage-szolgáltatás, például az Azure Blob storage. Ha többet szeretne megtudni az Azure storage-szolgáltatás titkosításáról (SSE), olvassa el [az Azure storage titkosítása az inaktív adatokat.](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)