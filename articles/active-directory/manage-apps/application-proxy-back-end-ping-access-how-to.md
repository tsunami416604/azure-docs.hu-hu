---
title: Alkalmazásproxy-alkalmazás beállítása a PingAccess használatára
description: Megtudhatja, hogy a PingAccess használatával hogyan bővítheti ki az alkalmazásproxy előnyeit a fejlécalapú hitelesítést használó alkalmazásokra
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c852c69b6f64b59845b551202bd1a25646a49eb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275657"
---
# <a name="how-to-configure-an-application-proxy-application-to-use-pingaccess"></a>Alkalmazásproxy-alkalmazás beállítása a PingAccess használatára

A PingAccess-szel való együttműködésünk lehetővé teszi, hogy az alkalmazásproxy előnyeit kiterjessze a fejlécalapú hitelesítést használó alkalmazásokra. Ha az alkalmazások nem használnak fejléceket, az [egyszeri bejelentkezésdokumentációjában](application-proxy-configure-single-sign-on-with-kcd.md) további részleteket talál az egyéb lehetőségekről.

## <a name="overview-of-steps-and-recommended-documents"></a>A lépések és az ajánlott dokumentumok áttekintése

Ha egy alkalmazást a PingAccess programmal szeretne konfigurálni, négy lépésből áll:

1.  Alkalmazásproxy-összekötők konfigurálása

2.  Azure AD alkalmazásproxy-alkalmazás létrehozása

3.  Letöltés & PingAccess konfigurálása

4.  Alkalmazások konfigurálása a PingAccess programban

Az egyes lépésekről az [Egyszeri bejelentkezés a fejlécekkel című dokumentációban](application-proxy-configure-single-sign-on-with-ping-access.md)olvashat részletesen.
