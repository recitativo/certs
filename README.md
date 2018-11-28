自己証明書とキーストアの作成
--------------------------

# 認証局作成
openssl req -new -config openssl.cnf -subj "/C=JP/ST=Tokyo/O=localhost/CN=localhost" -x509 -keyout ca.key -out ca.crt

    password

# サーバ鍵／証明書要求作成
openssl genrsa 2048 > sites/localhost/localhost.key
openssl req -new -days 36500 -subj "/C=JP/ST=Tokyo/O=localhost/CN=localhost" -key sites/localhost/localhost.key > sites/localhost/localhost.csr

# サーバ証明書作成
openssl ca -config openssl.cnf -days 36500 -keyfile ca.key -cert ca.crt -in sites/localhost/localhost.csr -out sites/localhost/localhost.crt

# キーストア作成
openssl pkcs12 -inkey sites/localhost/localhost.key -in sites/localhost/localhost.crt -export -out sites/localhost/localhost.pkcs12


# Java 用 keystore 作成
cd sites/localhost

## 既存キーの確認
keytool -list -keystore localhost.keystore -storepass localhost

## 既存キーの削除
keytool -delete -keystore localhost.keystore -storepass localhost -alias localhost

## 新規キーストアのインポート
keytool -importkeystore -srckeystore localhost.pkcs12 -srcstoretype PKCS12 -destkeystore localhost.keystore -srcalias 1 -destalias localhost
