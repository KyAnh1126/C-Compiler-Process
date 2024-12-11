source file mô tả ví dụ về dùng g++ để compile main.cpp, bao gồm các file như:
    - main.cpp là source file chính
    - thư mục include chứa header

I. Sơ lược về các files trong C/C++:
- source file: file nguồn của user, chứa các đoạn mã cần thiết để biên dịch, có đuôi .c hoặc .cpp
- header file: abstract file, chứa các hàm được khai báo và có thể được định nghĩa bởi các file triển khai, có đuôi .h hoặc .hpp 
- object file: file chứa mã nhị phân, được biên dịch từ source code của user, nhưng không thực thi được & có thể không chứa hàm main
- executable file: file thực thi, gồm một hoặc nhiều object files được link lại với nhau bởi linker, là kết quả cuối cùng của ứng dụng
- .a file: file chứa mã được biên dịch từ thư viện tĩnh, chứa tập hợp các object files
- .so file: file chứa mã được biên dịch từ thư viện động, chứa tập hợp các object files

    - các file mã máy của thư viện (thường có dạng .a hoặc .so) thường được đặt ở /usr/lib hoặc /usr/local/lib và gcc (hoặc g++) tự động tìm đến các file đó mặc định nên user không cần chỉ định hay link tới các file exe đó
    - tương tự với file exe, các file include (ie: stdio.h) thường được đặt ở /usr/include hoặc /usr/local/include  
    - và với các macro như "#include <stdio.h>" thì gcc tự động tìm header files cũng như library files trong các thư mục hệ thống trên và linker sẽ link tới các file mã máy được tìm thấy

II. Các flag phổ biến trong C/C++ và ví dụ:
1. -Wall: bật cờ cảnh báo của compiler
    - ví dụ: g++ -Wall main.cpp -o main
2. -Werror: chuyển warning thành error
    - ví dụ; g++ -Werror main.cpp -o main
3. -o: chỉ định file đầu ra (exe file) của source file
    - ví dụ: g++ main.cpp -o main
4. -g: hiển thị thông tin debug, có thể dùng cho gdb sau này
    - ví dụ: g++ -g main.cpp -o main
             gdb ./main (debug chương trình với gdb)
5. -O: tối ưu hóa chương trình
6. -std: chỉ định phiên bản của C
7. -c: compile source file, nhưng không link cũng như chỉ dừng lại ở object file và không tạo exe file
    - ví dụ: g++ -c main.cpp : tạo file main.o và có thể được dùng để link với các file khác
8. -I: trỏ tới thư mục chứa header files mong muốn
    - ví dụ: g++ -c -I include main.cpp
        + trỏ tới thư mục include, user không cần khai báo đường dẫn cụ thể ở #include

9. -L: trỏ tới thư mục chứa library files như .a file và .so file
    - ví dụ: g++ -c -L lib main.cpp
        + trỏ tới thư mục lib, g++ compiler tự tìm đến thư mục lib để tìm các file thư viện cần thiết
10. -l: link tới một library file nào đó như .o file hoặc .a file hoặc .so file 
    - ví dụ: g++ -c -lmessage main.cpp
        + link thư viện libmessage.a (có thể bỏ lib và .a) tới main.cpp

- ví dụ về cách compile main.cpp thành file thực thi main:
    + g++ -c message2.cpp : tạo message2.o
    + ar rcs libmessage2.a message2.o : tạo thư viện libmessage2.a từ message2.o
    + làm tương tự với message.cpp
    + g++ -I include -I lib -L lib  main.cpp -lmessage -lmessage2 -o main
        + -I include -I lib trỏ tới 2 thư mục chứa các file header là message.hpp và message2.hpp
        + -L lib trỏ tới thư mục chứa các file thư viện như libmessage.a và libmessage2.a
        + -lmessage và -lmessage2 link các file thư viện như libmessage.a và libmessage2.a
        + -o chỉ định tên file đầu ra mong muốn (mặc định là a.out)

III. Hành trình biên dịch code C:
- g++ -Wall -save-temps -I include -I lib -L lib  main.cpp -lmessage -lmessage2 -o main
    + -save-temps: lưu lại các tệp tạm thời trong quá trình biên dịch file main.cpp
        + bao gồm .i, .o và .s file
1. Preprocessor: 
    - tiền xử lý, công việc đầu tiên khi compile code C, bao gồm:
        + lược bỏ các comment trong main.cpp
        + thay thế các macro (ie: #define PI 3.14)
        + mở rộng các file được include (ie: #include "message.hpp")
    - generate .ii file (ie: main.ii)

2. Compiling:
    - biên dịch mã nguồn thành mã assembly
    - generate .s file (ie: main.s) chứa mã assembly

3. Assembly:
    - generate .o file (ie: main.o) từ .s file ở bước trên, chứa mã nhị phân 

4. Linking:
    - Ở các bước trước, g++ compiler chỉ mới tìm thấy khai báo của các hàm như message() hay message2() nhưng chưa biết được các định nghĩa của các hàm đó
    - Linking link đến các file chứa mã máy (ie: -lmessage) để tìm đến định nghĩa và địa chỉ của các hàm (ie: message())
