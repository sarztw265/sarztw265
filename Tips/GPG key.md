# GPG key

遠程連線的認證除了SSH金鑰外也可以透過特定類型的GPG金鑰操作，以下是Github官方的GPG金鑰產生教學

https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key
https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account

## 使用GPG金鑰與GitHub連線
### 下載GPG cml tool
到 https://www.gnupg.org/download/ 下方的 `GnuPG binary releases` 區塊下載作業系統對應的工具，以Windows系統為例，下載Gpg4win，預設安裝完畢後除了gpg，還會包含一個名為Kleopatra的工具，其本身也包含視覺化界面和加解密功能：
https://apps.kde.org/zh-tw/kleopatra/

這時可以回到cml執行產生金鑰了，Windows系統下建議使用git bash

### 產生金鑰
執行以下命令
`gpg --full-generate-key`
接著會需要選擇編碼類型、金鑰大小、效期和驗證使用者資訊，注意使用者資訊中提供的 email 必須和 GitHub 驗證過的是同一個，最後還會需要設定一組密碼片語 (passphrase)用以驗證身分，完成後就會得到金鑰

接著執行以下命令查看目前擁有的公私鑰
`gpg --list-secret-keys --keyid-format=long
`
```
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/c/Users/sky93/.gnupg/pubring.kbx
---------------------------------
sec   rsa3072/**************** 2024-02-03 [SC]
      ****************************************
uid                 [ultimate] sarztw265 (github) <sarzchen.tw@gmail.com>
ssb   rsa3072/**************** 2024-02-03 [E]
```

`sec` 欄位在金鑰編碼後方的英數字就是公鑰ID和生成日期，後方的`[SC]` 代表該金鑰對具有同時具有簽名（S）和加密（C）功能；下一行以 `*` 取代的內容是公鑰的驗證指紋

最下方的 `ssb` 區塊是次要金鑰(subkey)的公鑰，此處該金鑰僅能用於加密(encryption)

可透過指令獲公鑰的ASCII格式
`gpg --armor --export 218ECE91B68051CD`

若要使用該金鑰連線GitHub需要提供含 `[SC]` 功能公鑰的ASCII格式，也可將此結果另存為 `.gpg` 檔案以匯入Kleopatra管理

### 補充
https://blog.miniasp.com/post/2022/05/11/gnupg
