---
title: Az Azure Portal használata az ügyfél által kezelt kulcsok konfigurálásához
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat az Azure Key Vault használatával az Azure Portalon keresztül. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlők létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455257"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure Portal használatával

Az ügyfél által felügyelt kulcsok tárolásához az Azure Key Vault használatával kell tárolnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services-erőforrás és a key vault kell lennie ugyanabban a régióban és ugyanabban az Azure Active Directory (Azure AD) bérlőben, de lehetnek különböző előfizetések. Az Azure Key Vaultról a [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vault ügyfél által felügyelt kulcsok at az [Azure Portalon.](https://portal.azure.com/) Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure Portalon, olvassa el a [rövid útmutató: Az Azure Key Vault titkos kulcsának beállítása és beolvasása az Azure Portalon.](../../key-vault/secrets/quick-create-portal.md)

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok használatához két tulajdonságot kell beállítani a key vaultban, a **Helyreállítható törlés** és a **Ne ürítse ki a mezőt.** Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de egy új vagy meglévő kulcstartón a PowerShell vagy az Azure CLI használatával engedélyezhetők.

> [!IMPORTANT]
> Ha nem engedélyezve van a **Helyreállítható törlés** és a **Ne ürítés** tulajdonságok, és törli a kulcsot, nem fogja tudni helyreállítani az adatokat a Cognitive Service erőforrásban.

Ha tudni szeretné, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartóban, olvassa el a **"Helyreállítható törlés engedélyezése** és a **kiürítési védelem engedélyezése"** című szakaszokat az alábbi cikkek egyikében:

- [A soft-delete használata a PowerShell használatával.](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)
- [A soft-delete használata a CLI-vel.](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)

Csak a 2048-as méretű RSA-kulcsok at támogatja az Azure Storage titkosítása. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

## <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Keresse meg a Cognitive Services-erőforrást.
1. A Cognitive Services-erőforrás **Beállítások** paneljén kattintson a **Titkosítás gombra.** Válassza az **Ügyfél által kezelt kulcsok** lehetőséget, az alábbi ábrán látható módon.

    ![Képernyőkép az Ügyfél által kezelt kulcsok kiválasztásáról](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Kulcs megadása

Miután engedélyezte az ügyfél által felügyelt kulcsokat, megadhatja a Cognitive Services-erőforráshoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

Kulcs URI-ként történő megadásához kövesse az alábbi lépéseket:

1. A kulcs URI-jának megkereséséhez az Azure Portalon keresse meg a key vaultot, és válassza a **Kulcsok** beállítást. Jelölje ki a kívánt kulcsot, majd kattintson a billentyűre a verzióinak megtekintéséhez. Válassza ki a kulcsverziót az adott verzió beállításainak megtekintéséhez.
1. Másolja az URI-t megadó **Kulcsazonosító** mező értékét.

    ![Képernyőkép a kulcstartó kulcsURI-jának megjelenítéséről](../media/cognitive-services-encryption/key-uri-portal.png)

1. A **tárfiók titkosítási** beállításai ban válassza a **Kulcs URI megadása** lehetőséget.
1. Illessze be a Kulcs URI-mezőbe másolt **URI-t.**

   ![Képernyőkép a kulcs URI-jának beírásáról](../media/cognitive-services-encryption/ssecmk2.png)

1. Adja meg a key vaultot tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása a kulcstartóból

Kulcs megadása egy kulcstartóból, először győződjön meg arról, hogy rendelkezik egy kulcstartót tartalmazó. Kulcs megadása egy kulcstartóból, kövesse az alábbi lépéseket:

1. Válassza a **Kijelölés a Key Vaultból** lehetőséget.
1. Jelölje ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását ábrázoló képernyőkép](../media/cognitive-services-encryption/ssecmk3.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítse a Cognitive Services erőforrást az új verzió használatához. Kövesse az alábbi lépéseket:

1. Nyissa meg a Cognitive Services-erőforrást, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcsverzió URI-ját. Másik lehetőségként a kulcstartót és a kulcsot is kijelölheti a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

A titkosításhoz használt kulcs módosításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Cognitive Services-erőforrást, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-ját. Másik lehetőségként kiválaszthatja a key vaultot, és választhat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="disable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Cognitive Services-erőforrás ezután microsoftáltal kezelt kulcsokkal titkosítva lesz. Az ügyfél által kezelt kulcsok letiltásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Cognitive Services-erőforrást, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a jelet a **Saját kulcsbeállítás** melletti jelölőnégyzetből.

## <a name="next-steps"></a>További lépések

* [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Cognitive Services ügyféláltal felügyelt kulcskérelem-űrlapja](https://aka.ms/cogsvc-cmk)
* [Face Services titkosítása adatok inaktív](../Face/face-encryption-of-data-at-rest.md)
* [A nyugalmi adatok QnA Maker titkosítása](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Az inaktív adatok nyelvtudása szolgáltatástitkosítása](../LUIS/luis-encryption-of-data-at-rest.md)
* [Tartalommoderátor inaktív adatok titkosítása](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Az inaktív adatok fordítótitkosítása](../translator/translator-encryption-of-data-at-rest.md)
* [Az inaktív adatok személyre szabása](../personalizer/personalizer-encryption-of-data-at-rest.md)
