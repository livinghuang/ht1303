## LoRa 集中器安裝指南：逐步操作指南

**介紹：**
LoRa 集中器是 LoRaWAN 網路中的關鍵元件，用於與多個 LoRa 設備進行高效通訊。在本教程中，我們將逐步介紹如何安裝 LoRa 集中器，使用 Semtech 晶片 SX1303 和 HT1303 硬體板，以確保建立一個穩定可靠的網路。

**前提條件：**
在開始安裝過程之前，請確保您具備以下條件：

1. 一塊 HT1303 硬體板與對應mPCI to Raspberry Pi的轉接板，搭載 Semtech 晶片 SX1303。
2. 一台運行基於 Linux 的電腦或單板電腦（SBC）。
3. 用於下載必要軟體和更新的網際網路連接。
4. 對 Linux 終端命令的基本了解。

### 步驟1：硬體設置

1. 使用 SPI 介面將 HT1303 硬體板連接到電腦或 SBC。
2. 確保所有必需的電源連接正確連接（請參考硬體板的文件以瞭解詳細資訊）。

### 步驟2：準備作業系統

1. 在電腦或 SBC 上安裝基於 Linux 的作業系統（例如 Raspbian 適用於 Raspberry Pi）。
2. 開啟終端機並執行以下命令以更新作業系統：
   ```
   sudo apt update
   sudo apt upgrade
   ```

### 步驟3：安裝所需軟體

1. 開啟終端機並執行以下命令以安裝必要的軟體套件：
   ```
   sudo apt install git build-essential
   ```
2. 複製 HT1303 硬體板的 GitHub 儲存庫：
   ```
   git clone https://github.com/livinghuang/ht1303.git
   ```

### 步驟4：配置 LoRa 集中器

1. 進入複製的儲存庫目錄：
   ```
   cd ht1303
   ```

2. 解壓縮 `sx130x_hal.tar` 壓縮檔：
   ```
   tar -xvf sx130x_hal.tar
   ```

3. 進入解壓縮後的目錄：
   ```
   cd sx130x_hal/
   ```

4. 編譯軟體：
   ```


   make clean all
   ```

5. 進入 `packet_forwarder` 目錄：
   ```
   cd packet_forwarder/
   ```

在此目錄中，您可以找到多個 `configure.json` 檔案。這些是由 Lora-net（Semtech 官方專案）為各個區域頻率設定的範例。我們已經在儲存庫中提供了一個 `global_conf.json` 檔案，該檔案適用於台灣區域。您可以使用以下命令檢查該檔案：
   ```
   cat ~/ht1303/global.conf
   ```

7. 將可執行檔複製到使用者家目錄下的 `ht1303` 目錄：
   ```
   cp lora_pkt_fwd ~/ht1303
   ```

7. 返回 `ht1303` 目錄：
   ```
   cd ~/ht1303
   ```

8. 給予 `reset_lgw.sh` 腳本執行權限：
   ```
   chmod +x reset_lgw.sh
   ```

由於 SX1303 在啟動之前需要重置信號，我們的轉接板連接到 GPIO17。您可以通過以下命令檢查此腳本：
   ```
   sudo nano reset_lgw.sh
   ```

您可能會找到以下文本：
   ```
   SX1302_RESET_PIN=17   # SX1302 reset
   ```

如果不是 17，請將其修改為 17。如果您使用其他轉接板，請確保將重置引腳連接到 Raspberry Pi 上的正確 GPIO，然後設置為正確的引腳。

與 `global_conf.json` 一樣，我們已經修改了儲存庫中的腳本。如果您使用的是由第三方 SILIQ 製造的 HT1303 轉接板，則無需修改此腳本，但仍需將其設置為可執行檔。

9. 測試 LoRa 封包轉發程式：
   ```
   ./lora_pkt_fwd
   ```

現在，我們可以測試封包轉發程式。

10. 將服務檔案複製到適當的目錄：
    ```
    sudo cp lora_pkt_fwd.service /etc/systemd/system
    ```

在接下來的步驟（10、11、12、13）中，我們將使封包轉發程式在系統啟動時自動運行，而無需手動執行 `./lora_pkt_fwd` 。

11. 啟動 LoRa 封包轉發服務：
    ```
    sudo systemctl start lora_pkt_fwd.service
    ```

12. 檢查 LoRa 封包轉發服務的

狀態：
    ```
    sudo systemctl status lora_pkt_fwd.service
    ```

13. 啟用 LoRa 封包轉發服務，使其在系統啟動時自動運行：
    ```
    sudo systemctl enable lora_pkt_fwd.service
    ```

現在，我們已啟用 Semtech UDP Lora 封包轉發服務。另外，在未來，您可能需要檢查服務的日誌。您可以使用以下命令輕鬆檢查該服務的日誌：
    ```
    journalctl -u lora_pkt_fwd.service -f -n 50
    ```

14. 重新啟動系統以應用更改：
    ```
    sudo reboot
    ```

請仔細按照這些步驟進行，以成功安裝和配置使用 SX1303 和 HT1303 硬體板的 LoRa 集中器。
