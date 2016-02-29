您可以使用 Visual Studio 建立 Azure 儲存體佇列 **伺服器總管**。

![伺服器總管 Blob][Image1]

1. 在 **檢視** ] 功能表上，選擇 [ **伺服器總管**。
2. 在 [伺服器總管] 中，展開 **Azure** 展開節點，針對您的訂閱， **儲存體** 節點以及您在 Azure 儲存體中指定的儲存體帳戶的節點連接的服務。
3. 選取 **佇列** 節點，然後選擇 **建立佇列** 從內容功能表。
4. 輸入容器的名稱，然後選擇 **確定**。   

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。 如果您想要讓公用容器中的檔案，選取容器中的 **伺服器總管** 按 `F4` 顯示 **屬性** 視窗。 設定 **公用讀取權限** 至 **Blob**。 網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png
