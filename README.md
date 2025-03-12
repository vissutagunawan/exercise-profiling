# Reflection

# Performance Testing and Profiling

This repository contains the results of performance testing and profiling experiments using Apache JMeter and IntelliJ Profiler to optimize Java application code.

## Before Optimization

The initial code showed significant performance issues when tested under load:

/all-students before profiling
![All Students 1 - Original](./images/Screenshot%202025-03-12%20125820.png)

![All Students 2 - Original](./images/Screenshot%202025-03-12%20125831.png)

![All Students 3 - Original](./images/Screenshot%202025-03-12%20125843.png)

![All Students 4 - Original](./images/Screenshot%202025-03-12%20125855.png)

![All Students 5 - Original](./images/Screenshot%202025-03-12%20132407.png)

/all-students-name before profiling
![All Students Name 1 - Original](./images/Screenshot%202025-03-12%20125927.png)

![All Students Name 2 - Original](./images/Screenshot%202025-03-12%20125938.png)

![All Students Name 3 - Original](./images/Screenshot%202025-03-12%20125949.png)

![All Students Name 4 - Original](./images/Screenshot%202025-03-12%20130001.png)

![All Students Name 5 - Original](./images/Screenshot%202025-03-12%20132524.png)

/highest-gpa before profiling
![Highest GPA 1 - Original ](./images/Screenshot%202025-03-12%20130102.png)

![Highest GPA 2 - Original ](./images/Screenshot%202025-03-12%20130114.png)

![Highest GPA 3 - Original ](./images/Screenshot%202025-03-12%20130125.png)

![Highest GPA 4 - Original ](./images/Screenshot%202025-03-12%20130136.png)

![Highest GPA 5 - Original](./images/Screenshot%202025-03-12%20132605.png)


### Original Code

```java
public List<StudentCourse> getAllStudentsWithCourses() {
    List<Student> students = studentRepository.findAll();
    List<StudentCourse> studentCourses = new ArrayList<>();
    for (Student student : students) {
        List<StudentCourse> studentCoursesByStudent = studentCourseRepository.findByStudentId(student.getId());
        for (StudentCourse studentCourseByStudent : studentCoursesByStudent) {
            StudentCourse studentCourse = new StudentCourse();
            studentCourse.setStudent(student);
            studentCourse.setCourse(studentCourseByStudent.getCourse());
            studentCourses.add(studentCourse);
        }
    }
    return studentCourses;
}

public Optional<Student> findStudentWithHighestGpa() {
    List<Student> students = studentRepository.findAll();
    Student highestGpaStudent = null;
    double highestGpa = 0.0;
    for (Student student : students) {
        if (student.getGpa() > highestGpa) {
            highestGpa = student.getGpa();
            highestGpaStudent = student;
        }
    }
    return Optional.ofNullable(highestGpaStudent);
}

public String joinStudentNames() {
    List<Student> students = studentRepository.findAll();
    String result = "";
    for (Student student : students) {
        result += student.getName() + ", ";
    }
    return result.substring(0, result.length() - 2);
}
```

## After Optimization

After applying profiling insights, the code was optimized and showed significant performance improvements:

/all-students after profiling
![All Students 1 - Optimized](./images/Screenshot%202025-03-12%20142747.png)

![All Students 2 - Optimized](./images/Screenshot%202025-03-12%20142756.png)

![All Students 3 - Optimized](./images/Screenshot%202025-03-12%20142806.png)

![All Students 4 - Optimized ](./images/Screenshot%202025-03-12%20142819.png)

![All Students 5 - Optimized ](./images/Screenshot%2025-03-12%20193535.png)

/all-students-name after profiling
![All Students Name 1 - Optimized ](./images/Screenshot%202025-03-12%20143204.png)

![All Students Name 2 - Optimized ](./images/Screenshot%202025-03-12%20143212.png)

![All Students Name 3 - Optimized ](./images/Screenshot%202025-03-12%20143220.png)

![All Students Name 4 - Optimized ](./images/Screenshot%202025-03-12%20143228.png)

![All Students Name 5 - Optimized ](./images/Screenshot%202025-03-12%20193552.png)

/highest-gpa after profiling
![Highest GPA 1 - Optimized ](./images/Screenshot%202025-03-12%20143359.png)

![Highest GPA 2 - Optimized ](./images/Screenshot%202025-03-12%20143408.png)

![Highest GPA 3 - Optimized ](./images/Screenshot%202025-03-12%20143417.png)

![Highest GPA 4 - Optimized ](./images/Screenshot%202025-03-12%20143426.png)

![Highest GPA 5 - Optimized](./images/Screenshot%202025-03-12%20193611.png)


### Optimized Code

```java
public List<StudentCourse> getAllStudentsWithCourses() {
    return studentCourseRepository.findAll();
}

public Optional<Student> findStudentWithHighestGpa() {
    return studentRepository.findAll()
            .stream()
            .sorted(Comparator.comparing(Student::getGpa).reversed().thenComparing(Student::getName))
            .findFirst();
}

public String joinStudentNames() {
    List<Student> students = studentRepository.findAll();
    return students.stream()
            .map(Student::getName)
            .collect(Collectors.joining(", "));
}
```

> 1. What is the difference between the approach of performance testing with JMeter and profiling with IntelliJ Profiler in the context of optimizing application performance?

Performance testing dengan JMeter dan profiling dengan IntelliJ Profiler memiliki pendekatan yang berbeda dalam konteks optimasi performa aplikasi. JMeter berfokus pada pengujian beban eksternal, dimana kita dapat mensimulasikan banyak pengguna yang mengakses aplikasi secara bersamaan untuk melihat bagaimana sistem merespons di bawah kondisi beban tertentu. Hal ini terlihat dari screenshot di atas dimana JMeter menampilkan hasil dari berbagai thread group dengan label seperti "all-student request" dan "highest-gpa request".
Sementara itu, IntelliJ Profiler lebih berfokus pada analisis internal kode, mengidentifikasi metode mana yang menghabiskan waktu eksekusi terbanyak, alokasi memori, dan bottleneck lainnya pada level kode. Pendekatan ini memungkinkan developer untuk melihat secara tepat bagian kode mana yang perlu dioptimasi, seperti yang terlihat pada perubahan kode yang dilakukan, misalnya mengubah penggabungan string menggunakan operator '+' menjadi stream collector yang lebih efisien.

> 2. How does the profiling process help you in identifying and understanding the weak points in your application?

Proses profiling membantu mengidentifikasi titik lemah aplikasi dengan cara memberikan gambaran rinci tentang bagaimana kode dieksekusi secara real-time. Profiler merekam berbagai metrik seperti waktu eksekusi metode, alokasi memori, garbage collection, dan penggunaan CPU. Dengan informasi ini, developer dapat melihat dengan jelas metode mana yang menjadi bottleneck, pola akses database yang tidak efisien, atau alokasi memori yang berlebihan.
Dalam kasus kode StudentService, profiling kemungkinan menunjukkan bahwa metode joinStudentNames() yang menggunakan operator '+' untuk menggabungkan string adalah tidak efisien karena setiap penggabungan menciptakan objek String baru di memori. Begitu juga dengan metode getAllStudentsWithCourses() yang melakukan iterasi manual dan membuat objek baru berulang kali, serta metode findStudentWithHighestGpa() yang menggunakan pendekatan manual untuk mencari nilai maksimum. Profiling membantu mengidentifikasi masalah-masalah ini sehingga dapat dioptimasi seperti yang terlihat pada kode setelah dioptimasi.

> 3. Do you think IntelliJ Profiler is effective in assisting you to analyze and identify bottlenecks in your application code?

IntelliJ Profiler cukup efektif dalam membantu menganalisis dan mengidentifikasi bottleneck dalam kode aplikasi. Berdasarkan optimasi yang telah dilakukan, terlihat bahwa IntelliJ Profiler mampu memberikan wawasan yang berharga tentang area-area yang perlu ditingkatkan. Tool ini menyediakan visualisasi grafis dari hotspot performa, call tree, dan informasi alokasi memori yang memudahkan developer memahami bagaimana kode mereka berperilaku saat runtime.
Efektivitas IntelliJ Profiler juga terlihat dari kemampuannya untuk menunjukkan masalah konkret seperti operasi string yang tidak efisien, loop bersarang yang dapat dioptimasi, atau meningkatnya penggunaan memori. Hal ini memungkinkan pengoptimalan yang ditargetkan, seperti yang terlihat pada perubahan Anda untuk menggunakan API Stream Java dan metode statis dari repositori alih-alih logika manual yang kurang efisien.

> 4. What are the main challenges you face when conducting performance testing and profiling, and how do you overcome these challenges?

Tantangan utama dalam melakukan performance testing dan profiling adalah menginterpretasikan hasil dengan benar dan menentukan perubahan mana yang akan memberikan dampak terbesar pada performa. Seringkali, bottleneck tidak selalu jelas dan mungkin memerlukan beberapa iterasi testing untuk mengidentifikasinya dengan tepat. Selain itu, kondisi testing yang berbeda dengan kondisi produksi dapat menyebabkan kesimpulan yang tidak akurat.
Cara mengatasi tantangan ini adalah dengan membangun test case yang mendekati kondisi produksi, melakukan baseline testing sebelum melakukan perubahan, dan melakukan pengujian secara bertahap. Penting juga untuk memahami bahwa performa aplikasi adalah hasil dari banyak faktor, tidak hanya kode aplikasi tetapi juga konfigurasi server, database, dan infrastruktur.
Tantangan lain adalah overhead yang ditimbulkan oleh tools profiling itu sendiri, yang dapat mempengaruhi hasil pengukuran. Untuk mengatasi ini, penting untuk memverifikasi temuan dengan pengujian terpisah menggunakan tools seperti JMeter dan selalu melakukan pengujian baseline untuk memastikan bahwa perubahan yang dilakukan benar-benar meningkatkan performa.

> 5. What are the main benefits you gain from using IntelliJ Profiler for profiling your application code?

Manfaat utama dari menggunakan IntelliJ Profiler untuk profiling kode aplikasi adalah integrasi yang mulus dengan lingkungan pengembangan, yang memungkinkan analisis langsung tanpa meninggalkan IDE. Ini menghemat waktu dan mempermudah proses debugging performa.
IntelliJ Profiler juga menyediakan visualisasi data yang kaya, memungkinkan developer untuk melihat hotspot performa, call tree, dan alokasi memori dalam format yang mudah dipahami. Tool ini dapat menyoroti metode yang menghabiskan waktu terbanyak, membantu mengidentifikasi bottleneck dengan cepat. Seperti yang terlihat pada optimasi yang dilakukan, IntelliJ Profiler membantu mengidentifikasi ineffisiensi pada penggabungan string dan iterasi berulang, yang kemudian dioptimasi menggunakan fitur Java modern seperti Stream API.
Manfaat lainnya adalah kemampuan untuk membandingkan snapshot profiling dari waktu ke waktu, memungkinkan developer melihat apakah perubahan yang mereka buat benar-benar meningkatkan performa. Ini sangat membantu dalam siklus pengembangan iteratif.

> 6. How do you handle situations where the results from profiling with IntelliJ Profiler are not entirely consistent with findings from performance testing using JMeter?

Ketika hasil profiling dengan IntelliJ Profiler tidak konsisten dengan temuan dari JMeter, penting untuk memahami bahwa kedua alat tersebut mengukur aspek yang berbeda dari performa aplikasi. IntelliJ Profiler berfokus pada performa internal kode, sementara JMeter lebih melihat pada respons sistem secara keseluruhan di bawah beban.
Untuk menangani ketidakkonsistenan ini, pertama-tama perlu menganalisis penyebabnya. Apakah ada faktor eksternal yang mempengaruhi hasil JMeter, seperti bottleneck jaringan atau database? Apakah ada perbedaan dalam kondisi testing antara kedua alat? Sering kali, optimasi yang baik pada level kode mungkin tidak terlihat signifikan pada hasil JMeter jika bottleneck utama sebenarnya ada pada komponen lain seperti database atau jaringan.
Pendekatan terbaik adalah dengan melakukan pengujian yang lebih terisolasi untuk mengkonfirmasi temuan dari masing-masing alat. Misalnya, jika IntelliJ Profiler menunjukkan metode tertentu lambat tetapi JMeter tidak menunjukkan perbaikan setelah optimasi, isolasi metode tersebut dalam benchmark terpisah dapat membantu memverifikasi apakah optimasi memang efektif.

> 7. What strategies do you implement in optimizing application code after analyzing results from performance testing and profiling? How do you ensure the changes you make do not affect the application's functionality?

Berdasarkan kode yang dioptimasi, beberapa strategi yang diimplementasikan dalam mengoptimasi kode aplikasi setelah menganalisis hasil performance testing dan profiling meliputi:
- Menggunakan Java Stream API untuk operasi data yang lebih efisien, seperti pada metode findStudentWithHighestGpa() yang sekarang menggunakan stream dan comparator.
- Menghindari operasi string yang tidak efisien dengan menggunakan Collectors.joining() pada metode joinStudentNames().
- Memanfaatkan metode repositori yang sudah ada, seperti menggunakan studentCourseRepository.findAll() langsung daripada melakukan iterasi manual pada metode getAllStudentsWithCourses().