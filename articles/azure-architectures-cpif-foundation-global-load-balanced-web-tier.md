<properties 
   pageTitle="全域負載平衡 Web 層 (Azure 架構模式)" 
   description="全域負載平衡 Web 層模式是基礎區域的一部分，CPIF 架構文件中有相關的詳細說明。" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>


# 全域負載平衡 Web 層 (Azure 架構模式)



CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。

**全域負載平衡 Web 層**模式是**基礎**區域的一部分，CPIF 架構文件中有相關的詳細說明。

## 全域負載平衡 Web 層

全域負載平衡 Web 層設計模式詳述供應 Web 層服務所需的 Azure 功能和服務；Web 層服務能跨越地理界限提供可預期的效能和高可用性。

有鑑於此設計模式的目的，Web 層的定義為以隔離方式或隨附於多層式 Web 應用程式的方式，提供傳統 HTTP/HTTPS 內容或應用程式服務的服務層。 在此模式中，Web 層的負載平衡可透過在本機區域內和跨區域等方式提供。 從計算的觀點來看，這些服務可透過 Microsoft Azure 虛擬機器、網站或兩者的組合來提供。 提供 Web 服務的虛擬機器可以在 Microsoft Azure 中使用所有支援的 Microsoft Windows 或 Linux 散發客體作業系統裝載內容。


## 架構模式概觀

本文件說明可透過多個地理位置提供 Web 服務或 Web 伺服器內容之存取權限，進而滿足可用性和備援能力之需求的模式。 下圖說明的關鍵服務並未著重在 Web 服務本身的 Web 平台條件約束或開發方法。 本模式有兩種變化：一種是將 Web 內容或服務裝載於虛擬機器上 (使用 Azure 支援的作業系統和系列)，另一種是使用 Azure 網站。 下圖以虛擬機器為例簡單說明相關服務，以及如何將它們依附在本模式中使用。

## 其他資源



## 另請參閱
















