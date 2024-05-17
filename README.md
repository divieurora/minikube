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

## Reflection - Rolling Update & Kubernetes Manifest File

1. What is the difference between Rolling Update and Recreate deployment strategy?

    Rolling Update dan Recreate deployment strategy memiliki perbedaan yang signifikan. Rolling Update ketika terdapat versi yang baru dapat secara sekaligus memperbarui instance-instancenya secara bertahap tanpa menghentikan instance dengan versi lamanya. Sedangkan pada Recreate, perlu penggantian seluruh instance ke versi yang baru secara bersamaan yang mengakibatkan downtime. Hal ini seumpama mengganti lampu yang mati, Rolling Update bersikap seperti rangkaian paralel yang tetap bekerja pada lampu yang masih menyala sedangkan Recreate bersikap seperti rangkaian seri yang akan ikut mati seluruhnya.

    Secara kompleksitas, Rolling Update lebih kompleks tetapi dapat meminimalkan downtime dan mempertahankan aplikasi. Sementara itu, Recreate lebih sederhana namun dapat mengakibatkan downtime pada aplikasi. Kedua strategi deployment ini dapat dipilih sesuai dengan konteks kegunaan deployment aplikasi.

2. Try deploying the Spring Petclinic REST using Recreate deployment strategy and document
your attempt.

    Berikut adalah cara saya melakukan deployment ulang dengan Recreate Deployment Strategy. 
    ![](/images/recreate-deployment.png)

    Pertama kali, setelah melakukan `minikube delete` pada cluster minikube sebelumnya, saya melakukan `minikube start` dan mengatur metrics-servernya dengan `minikube addons enable metrics-server` ulang. Kemudian melakukan `minikube apply` untuk kedua file yaitu recreate-deployment.yaml dan service.yaml. 

    Setelah melakukan apply untuk kedua file tersebut, saya mengecek pods yang sedang berjalan dengan command `minikube get pods`. Kemudian saya mengaktifkan service untuk spring-petclinic-rest untuk dapat diakses melalui localhost. Seperti pada tutorial Rollout Deployment, service petclinic akan muncul pada endpoint localhost/petclinic, dalam hal ini pada milik saya adalah:
    `http://127.0.0.1:50835/petclinic/`

3. Prepare different manifest files for executing Recreate deployment strategy.

    Sebelum melakukan apply file, saya melakukan edit pada file deployment.yaml yang dihasilkan dari tutorial Rollout Deployment. Bagian yang diedit hanya pada bagian `strategy` dan `type`, dari yang sebelumnya RollingUpdate menjadi Recreate. Berikut snippet code untuk update deployment strategy type pada recreate-deployment.yaml:
    ```
    ...
      strategy:
        type: Recreate
    ...
    ```

4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.

    File manifest kubernetes menyediakan deskripsi yang jelas tentang proses deployment aplikasi dan interaksi komponen-komponen di dalamnya. Dalam file manifest tersebut terdapat konfigurasi deployment, service, dan resource lainnya yang dapat mengotomatisasi proses deployment dan memfasilitasi manajemen versi deployment. Sehingga dengan menggunakan file manifest untuk melakukan deployment, deployment tersebut dapat menjadi lebih konsisten termasuk jika dilakukan di various environment. 

    Perbedaan yang signifikan terlihat ketika melakukan deployment secara manual dengan harus melakukan semua langkahnya secara manual. Dalam prosesnya sering terjadi kesalahan serta lebih sulit karena harus mengetahui cara konfigurasi yang tepat. Sehingga, penggunaan file manifest kubernetes sangat membanu proses deployment agar lebih efisien.