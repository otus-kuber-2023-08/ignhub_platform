Применяем StatefulSet с MinIO и шаблоном PVC, создаем headless service.

Проверка с помощью busybox
* запускаем Pod командой: k run --rm -ti test --image=busybox -- sh
* проверка доступности по имени: 
	ping minio.default.svc.cluster.local
  вывод: 
	PING minio.default.svc.cluster.local (10.244.3.2): 56 data bytes
	64 bytes from 10.244.3.2: seq=0 ttl=63 time=0.041 ms
	64 bytes from 10.244.3.2: seq=1 ttl=63 time=0.055 ms
* пробуем подключиться к порту: 
	nc 10.244.3.2 9000 -v
  вывод: 
	10.244.3.2 (10.244.3.2:9000) open
	HTTP/1.1 400 Bad Request
	Content-Type: text/plain; charset=utf-8
	Connection: close
	400 Bad Request
	
Запускаем утилиту управления minio client:
	k run test --rm -ti --image=minio/mc -- alias set minio http://minio.default.svc.cluster.local:9000
Вводим ключи для подключения:
	Enter Access Key: minio
	Enter Secret Key:
	Added `minio` successfully.
Работает.

Тест веб-морды MinIO Browser:
	k port-forward pods/minio-0 9000:9000
	http://localhost:9000
	
Задание со Звездочкой*
Создадим одно значение в секрете с помощью cli:
	> k create secret generic minio-secret --from-literal 'acces-key=minio'
Добавим в этот же секрет значение через yaml конфигурацию:
	> для этого закодируем пароль: echo -n 'minio789' | base64
	> применим k apply -f minio-secret.yaml
Проверяем: k get secrets minio-secret -o yaml
	apiVersion: v1
	data:
	  acces-key: bWluaW8=
	  secret-key: bWluaW83ODk=
	kind: Secret
Переделываем yaml minio StatefulSet, добавляем ссылки (secretKeyRef) на minio-secret.

