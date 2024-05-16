# Advance Programming Tutorial 11
Tutorial for Advanced Programming 2024 Module 11 - Faculty of Computer Science, Universitas Indonesia

---
## Reflection - Hello Minikube

1. Compare the application logs before and after you exposed it as a Service.

    Sebelum service `hello-node` di-_expose_ tampilan logs yang muncul adalah seperti ini:
    ![](/images/logs-before-exposed.png)
    Sedangkan dengan menjalankan _command_ `kubectl expose deployment hello-node  --type=LoadBalancer --port=8080`, deployment hello-node sudah di-_expose_ ke localhost.
    
    Tampilan logs yang muncul setelah service `hello-node` adalah seperti ini:
    ![](/images/logs-exposed-several-times.png)
    (Untuk memperoleh logs seperti pada gambar di atas, saya melakukan refresh pada app sebanyak dua kali.) 

    Setiap kali dilakukan run pada app, akan terbentuk _request_ baru melalui service yang sudah di-_expose_ sebelumnya. Sehingga, setiap kali app diakses log akan mencatat _request_ dan menambahkan entri log yang baru sesuai dengan interaksi user.

2. Notice that there are two versions of `kubectl get` invocation during this tutorial section. The first does not have any option, while the letter has `-n` option with value set to `kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you explicitly created?

    Perbedaan command `get` pada Kubernetes di tutorial ini terletak pada kedua command berikut:
    ```
    kubectl get services

    kubectl get pods,services -n kube-system
    ```

    Letter `-n` pada konteks ini digunakan untuk menentukan namespace dari mana objek akan diambil. Namespace di Kubernetes berfungsi untuk memisahkan objek seperti `pods`, `service`, dan `deployments` agar tidak saling tumpang tindih satu sama lain. Ketika command `kubectl` tidak menggunakan letter `-n`, maka namespace akan di-set dengan value default.
    Sedangkan ketika namespace di-set seperti dengan `kube-system`, maka objek yang diambil (dalam hal ini adalah pods) hanya diambil dari namespace `kube-system` saja. Jadi, ketika outputnya tidak memunculkan pods atau services yang sudah dibuat sebelumnya, itu berarti pods atau services tersebut berada di namespace yang berbeda.
