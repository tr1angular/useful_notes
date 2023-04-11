<h1 align="center">
<hr>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Cascadia+Code&size=25&pause=10000&color=E63946&center=true&width=435&lines=Public Key Infrastructure" alt="Typing SVG" /></a>
</h1>

<<<<<<< HEAD
1. Создание самоподписанных ключей и сертификатов.
=======
1. Создание ключей и сертификатов.
>>>>>>> cb7d7d1afc51316cc54aaa87e3db860af3a0259c
```bash
cat << EOF > "./san.conf"
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = yes

[req_distinguished_name]
C = RU
CN = 192.168.0.8

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = IP:192.168.0.8, DNS:localhost
EOF

# Сгенерировать ключ и сертификат центра сертификации (CA)
openssl genpkey -algorithm RSA -out rootCA.key
openssl req -x509 -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt

# Сгенерировать ключ клиента
openssl genpkey -algorithm RSA -out client.key

# Создать запрос на получение сертификата CSR (Certificate Sign Request)
openssl req -new -key client.key -out client.csr

# Подписать сертификат
openssl x509 -req -days 365 -in client.csr -CA rootCA.crt -CAkey rootCA.key \
    -CAcreateserial -out client.crt \
    -extfile san.conf \
    -extensions v3_req

# Проверить, записался ли SAN (Subject Alternative Name) в сертификат
openssl x509 -text -noout -in client.crt | grep -A 1 "Subject Alternative Name"

## Ссылки
1. [OpenSSL: принципы работы, создание сертификатов, аудит](https://hackware.ru/?p=12982)
2. [Steps to generate CSR for SAN certificate with openssl](https://www.golinuxcloud.com/openssl-subject-alternative-name/)
3. [Subject Alternative Name](https://stackoverflow.com/questions/30977264/subject-alternative-name-not-present-in-certificate)
