<!--author=SharS last changed: 9/17/15-->

#### 掛接、初始化及格式化磁碟區

1. 啟動 Microsoft iSCSI 啟動器。

2. 在 **iSCSI 啟動器內容** 視窗，請在 **探索** 索引標籤上，按一下 [ **探索入口網站**。

3. 在 **探索目標入口網站** 對話方塊中，提供您的已啟用 iSCSI 的網路介面的 IP 位址，然後按一下 [ **確定**。 

4. 在 **iSCSI 啟動器內容** 視窗，請在 **目標** 索引標籤上，找出 **探索到目標**。 裝置狀態應該會顯示為 **非作用中**。

5. 選取目標裝置，然後按一下 **連接**。 在連接裝置之後，狀態應該變更為 **連線**。 (如需有關使用 Microsoft iSCSI 啟動器的詳細資訊，請參閱 [安裝和設定 Microsoft iSCSI Initiator][1])。

6. 您的 Windows 主機上按 Windows 標誌鍵 + X，然後再按一下 **執行**。 

7. 在 **執行** ] 對話方塊中，輸入 **Diskmgmt.msc**。 按一下 [ **確定**, ，而 **磁碟管理** 對話方塊隨即出現。 右窗格將會顯示主機上的磁碟區。

8. 在 **磁碟管理** ] 視窗中，掛接的磁碟區會出現如下圖所示。 以滑鼠右鍵按一下探索到的磁碟區 (按一下磁碟名稱)，然後按一下 [ **線上**。

     ![初始化格式化磁碟區](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 

9. 以滑鼠右鍵按一下磁碟區 (按一下磁碟名稱)，然後按一下 [ **初始化**。

10. 若要格式化簡單磁碟區，請執行下列步驟：
  1. 選取磁碟區，以滑鼠右鍵按一下它 (按一下正確的區域)，然後按一下 **新增簡單磁碟區**。
  2. 在 [新增簡單磁碟區] 精靈中，指定磁碟區大小和磁碟機代號，並將磁碟區設定為 NTFS 檔案系統。
  3. 指定 64 KB 的配置單位大小。 這個配置單位大小適用於 StorSimple 解決方案中所使用的重複資料刪除演算法。
  4. 執行快速格式化。

![可用的視訊](./media/storsimple-mount-initialize-format-volume/Video_icon.png) **可用的視訊**

若要看影片，示範如何掛接、 初始化及格式化 StorSimple 磁碟區，按一下 [ [這裡](http://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/)。

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

