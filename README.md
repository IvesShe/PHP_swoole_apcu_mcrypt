# PHP swoole、apcu、mcrypt 環境搭建

# swoole 安裝

下載swoole

http://pecl.php.net/package/swoole

![image](./images/20210107114915.png)

運行到一半上面的網站突然連不了了，直接去GitHub找

https://github.com/swoole/swoole-src/tags?after=v4.0.1

![image](./images/20210107115446.png)

官網捉的 - swoole-2.2.0.tgz

GitHub捉的 - swoole-src-2.2.0.tar.gz


## 版本要求及確認

若查版本已安裝且符合，即可跳過該依賴的安裝

- Linux內核版本2.3.32以上，如CentOS 6.6以上
```bash
uname -r
```
![image](./images/20210107122121.png)

- PHP 5.3.10以上版本
```bash
php -v
```
![image](./images/20210107122427.png)

- gcc 4.4以上版本
```bash
gcc --version
```
![image](./images/20210107122533.png)

- cmake2.4以上版本(下面有安裝方法)
```bash
cmake --version
```
![image](./images/20210107130142.png)

原來漏裝了cmake...

補裝完成，過程筆記放到下面去，直接往下拉到cmake安裝的部分，裝完再回來

![image](./images/20210107134418.png)

- autoconf(下面有安裝方法)

![image](./images/20210107135137.png)

- automake(下面有安裝方法)

![image](./images/20210107200428.png)

- perl(下面有安裝方法)

![image](./images/20210107200523.png)

## 安裝swoole

解壓

```bash
tar -zxvf swoole-2.2.0.tgz
```

安裝phpize，產生configure

```bash
# 要對應php的目錄修改
# 可以先使用find / -name phpize確認位置
/usr/bin/phpize
# 或
/usr/local/webserver/php/bin/phpize
```

![image](./images/20210107144314.png)

發現沒有產生configure檔

![image](./images/20210107144431.png)


上網爬文似手漏安裝了automake(安裝步驟放後面)

在安裝其它對應的依賴之後，再執行phpize，終於看到configure了

![image](./images/20210107161607.png)

編譯環境

```bash
# 要對應php的目錄修改
# 可以先使用find / -name php-config確認位置
./configure --with-php-config=/usr/local/webserver/php/bin/php-config
# 或
./configure --with-php-config=/usr/bin/php-config
```

![image](./images/20210107162026.png)

![image](./images/20210107162009.png)


編譯&安裝

```bash
make && make install
```

![image](./images/20210107162227.png)

![image](./images/20210107163121.png)

查看已經安裝好的php擴展文件

```bash
ls /usr/lib64/php/modules/
```

![image](./images/20210107163549.png)

### 設定配置檔(作法一)

找尋php.ini檔
```bash
find / -name php.ini
```
拷貝一份並在最下面加上擴展

```bash
extension = swoole.so
```

![image](./images/20210107180053.png)

存放到php-fpm的設定檔目錄(依自己安裝的目錄而有異動)

![image](./images/20210107175922.png)

再重啟php-fmp下指令php-m應該就可以看到swoole了!!

```bash
php -m
php -m|grep swoole
```

### 設定配置檔(作法二-VM)
```bash
cd /etc/php.d/
```
在/etc/php.d/隨意拷一個設定檔，改名成swoole.ini，並修改內容增加擴展

```bash
cp 20-mysqlnd.ini swoole.ini
vi swoole.ini
```

![image](./images/20210107171010.png)

![image](./images/20210107171303.png)

```bash
php -m
php -m|grep swoole
```

![image](./images/20210107194934.png)


# cmake安裝

下載camke源碼包

http://www.cmake.org/files/v2.8/cmake-2.8.10.2.tar.gz

![image](./images/20210107130435.png)

放到/usr/local/src/

![image](./images/20210107130804.png)

解壓

```bash
tar -zxvf cmake-2.8.10.2.tar.gz
```

進入目錄執行

```bash
./bootstrap
```

![image](./images/20210107131341.png)

![image](./images/20210107131947.png)

```bash
gmake
```

![image](./images/20210107132208.png)

![image](./images/20210107132409.png)

這邊連成功畫面也記錄，是擔心之後在不同的環境，不一定能順利成功，記錄完整方便之後的判斷

```bash
gmake install
```

![image](./images/20210107132740.png)

![image](./images/20210107132718.png)

安裝成功了
```bash
cmake --version
```
![image](./images/20210107134418.png)

# automake安裝

## 源碼安裝(放棄，直接往下拉看用yum安裝的方式)

下載

http://ftp.gnu.org/gnu/automake/automake-1.14.tar.gz

![image](./images/20210107151802.png)

解壓

```bash
tar -zxvf automake-1.14.tar.gz
```

進入目錄

```bash
./bootstrap.sh
```
![image](./images/20210107152259.png)


編譯環境

```bash
./configure
```

![image](./images/20210107152332.png)

編譯&執行

(這邊報錯，發現還要裝perl...)

```bash
make && make install
```

## yum安裝(autoconf、automake)

```bash
yum whatprovides autoconf automake
```

```bash
automake --version
```

```bash
autoconf --version
```

![image](./images/20210107161240.png)

# 安裝perl

編譯安裝的過程會有點久，莫名的一直補裝軟件，就不附圖了

## 源碼安裝(放棄，直接往下拉看用yum安裝的方式)

下載

http://www.cpan.org/src/5.0/perl-5.16.1.tar.gz


解壓

```bash
tar -zxvf perl-5.16.1.tar.gz
```

編譯環境

```bash
./Configure -des -Dprefix=/usr/local/perl
```

編譯

```bash
make
```

編譯測試

```bash
make test
```

安裝

```bash
make install
```

以上源碼安裝的方式不知道為何無法成功

放棄源碼安裝

## 使用yum安裝

```bash
yum install perl*
```


確認版本

```bash
perl -v
```

![image](./images/20210107155708.png)


# 安裝apcu

下載

https://pecl.php.net/package/APCU

![image](./images/20210107201058.png)

拷貝到對應目錄

![image](./images/20210107201354.png)

解壓

```bash
tar -zxvf apcu-5.1.10.tgz
```

找一下phpize的位置

```bash
find / -name phpize
```
![image](./images/20210107201650.png)

下指令產生configure檔案

```bash
/usr/local/webserver/php/bin/phpize
```

可與上張圖作對比，執行前與執行後
![image](./images/20210107201830.png)

找一下php-config的位置

```bash
find / -name php-config
```
在 /usr/local/webserver/php/bin/php-config

![image](./images/20210107202104.png)

編譯

```bash
# 根據自己的安裝目錄，選擇以下其中一個運行

./configure  --with-php-config=/usr/local/webserver/php/bin/php-config
# 或
./configure  --with-php-config=/usr/bin/php-config
```

![image](./images/20210107202311.png)

```bash
make
```

![image](./images/20210107202407.png)

```bash
make install
```

![image](./images/20210107202719.png)

### 設定配置檔(作法一)

找尋php.ini檔
```bash
find / -name php.ini
```
拷貝一份並在最下面加上擴展

```bash
extension = apcu.so
```


存放到php-fpm的設定檔目錄(依自己安裝的目錄而有異動)

![image](./images/20210107175922.png)

再重啟php-fmp下指令php-m應該就可以看到apcu了!!

```bash
php -m
php -m|grep apcu
```

成功了!!!
![image](./images/20210107211007.png)

### 設定配置檔(作法二-VM-未成功)
```bash
cd /etc/php.d/
```
在/etc/php.d/隨意拷一個設定檔，改名成apcu.ini，並修改內容增加擴展

```bash
cp 20-mysqlnd.ini apcu.ini
vi apcu.ini
```

![image](./images/20210107204349.png)
```bash
php -m
php -m|grep apcu
```

# 安裝mcrypt

下載libmcrypt,mhash,mcrypt

libmcrypt

https://sourceforge.net/projects/mcrypt/files/libmcrypt/2.5.8/

![image](./images/20210107223410.png)

mhash

https://sourceforge.net/projects/mcrypt/files/libmcrypt/2.5.8/

![image](./images/20210107223410.png)

mcrypt

https://pecl.php.net/package/mcrypt

![image](./images/20210107223155.png)

這邊會寫的比較簡單，因為已經經歷以上的摧殘

## 安裝mcrypt

解壓

找到phpize的目錄
```bash
find / -name php-config
```

進入目錄執行，產生configure檔案
```bash
/usr/local/webserver/php/bin/phpize
```

找到php-config的目錄
```bash
find / -name php-config
```

編譯環境
```bash
./configure --with-php-config=/usr/local/webserver/php/bin/php-config
```

發生報錯，提示要先安裝libmcrypt(安裝方法放下面)

安裝完libmcrypt再回頭來安裝

```bash
make
```

```bash
make install
```

順利安裝完成，重啟php-fpm再查看

![image](./images/20210107225359.png)

## 安裝libmcrypt

解壓

進入目錄ls

似乎已經有configure

```bash
./configure
```

```bash
make
```

```bash
make install
```

