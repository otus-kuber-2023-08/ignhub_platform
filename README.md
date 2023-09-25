## kubernetes-intro

#Задание: Почему все pod в namespace kube-system восстановились после удаления.
                         
* coredns-5d78c9869d-4bs2q - создан из Deployment через coredns ReplicaSet coredns-5d78c9869d, которая следит за состоянием её подов и количеством реплик.
* etcd-minikube - в спецификации пода указана priorityClassName: system-node-critical, что гарантирует его постояное нахождение в планировщике, а также restartPolicy: Always - политика перезапуска всех контейнеров в поде (Always - в случае сбоя и удачного завершения)
* Аналогично для: kube-apiserver-minikube, kube-controller-manager-minikube, kube-scheduler-minikube.
* kube-proxy-652ng - ownerReferences: DaemonSet, который также перезапускает "упавшие" поды.