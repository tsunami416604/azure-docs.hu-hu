
如需有關磁碟的詳細資訊，請參閱 [關於磁碟和虛擬機器的 Vhd](../articles/virtual-machines-disks-vhds.md)。

##<a id="attachempty"></a>做法：連接空的磁碟

新增資料磁碟的一個較為簡易方式是連接空的磁碟，因為 Azure 會為您建立 .vhd 檔案並將它儲存在儲存體帳戶中。

1. 按一下 [ **虛擬機器**, ，然後選取適當的虛擬機器。

2. 在命令列中，按一下 [ **附加**, ，然後按一下 [ **連接空的磁碟**。


    ![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.   **連接空的磁碟** ] 對話方塊隨即出現。


    ![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)


    Do the following:

    - In **File Name**, accept the default name or type another one for the .vhd file, which is used for the disk. The data disk uses an automatically generated name, even if you type another name for the .vhd file.

    - Type the **Size (GB)** of the data disk.

    - Click the check mark to finish.

4.  建立並連接資料磁碟之後，它會列在虛擬機器的儀表板。

    ![成功連接空的資料磁碟](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)
    
> [AZURE.NOTE]
> 新增新的資料磁碟之後，您必須登入虛擬機器並初始化磁碟，這樣虛擬機器才能使用磁碟來儲存資料。 

##<a id="attachexisting"></a>做法：連接現有磁碟

連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。 使用 [Add-azurevhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) 指令程式可將.vhd 檔案上傳至儲存體帳戶。 在建立並上傳 .vhd 檔案之後，就可將它連接至虛擬機器。

1. 按一下 [ **虛擬機器**, ，然後選取適當的虛擬機器。

2. 在命令列中，按一下 [ **附加**, ，然後選取 **連接磁碟**。


    ![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

    The **Attach Disk** dialog box appears.



    ![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. 選取要連接至虛擬機器的資料磁碟。

4. 按一下核取記號將資料磁碟連接至虛擬機器。

5.  連接資料磁碟之後，它會列在虛擬機器的儀表板。


    ![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)



