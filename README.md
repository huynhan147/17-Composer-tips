#  [Martin Hujer blog](/)

## 17 Lời khuyên để sử dụng Composer hiệu quả

2018-01-05

Mặc dù hầu hết các developer PHP đều biết cách sử dụng Composer, không phải tất cả họ đều sử dụng nó một cách hiệu quả hoặc theo cách tốt nhất có thể. Vì vậy, tôi đã quyết định tóm tắt những thứ quan trọng trong quy trình làm việc hàng ngày của tôi.

Triết lý của hầu hết các lời khuyên là _"Thực hiện nó một cách an toàn"_, có nghĩa là nếu có nhiều cách hơn để xử lý một cái gì đó, tôi sẽ sử dụng cách tiếp cận ít bị lỗi nhất.

## Lời khuyên #1: Đọc tài liệu

Tôi thực sự khuyên bạn làm điều này. [Tài liệu](https://getcomposer.org/doc/) rất tuyệt
và dành một vài giờ đọc nó sẽ giúp bạn tiết kiệm nhiều thời gian hơn trong thời gian dài. Bạn sẽ ngạc nhiên khi có rất nhiều thứ mà Composer có thể làm được.

## Tip #2: Hãy nhận ra sự khác biệt giữa một "project" và một "library"

Điều quan trọng cần biết, cho dù bạn đang tạo một _"project"_ hoặc một
_"library"_. Mỗi loại sẽ yêu cầu những bài thực hành riêng biệt.

Một thư viện là một gói có thể tái sử dụng, mà bạn sẽ thêm như một dependency (phụ thuộc) - như là
`symfony/symfony`, `doctrine/orm` hoặc
[`elasticsearch/elasticsearch`](https://github.com/elastic/elasticsearch-php).

Một project là một ứng dụng, phụ thuộc vào một số thư viện. Nó thường không thể tái sử dụng được (không có project nào có thể yêu cầu nó như là một phụ thuộc).
Ví dụ điển hình là trang web thương mại điện tử, hệ thống hỗ trợ khách hàng, v.v.

Tôi sẽ phân biệt giữa thư viện và một project trong những lời khuyên dưới đây.

## Lời khuyên #3: Sử dụng các phiên bản phụ thuộc cụ thể cho các ứng dụng

Nếu bạn đang tạo một ứng dụng, bạn nên sử dụng phiên bản cụ thể nhất để xác định các phụ thuộc. Nếu bạn cần phân tích các file YAML, bạn nên chỉ định phụ thuộc như thế này `"symfony/yaml": "4.0.2"`.

Ngay cả khi thư viện tuân theo [Semantic Versioning](https://semver.org/), có thể backwards-compatibility trong các phiên bản nhỏ và bản vá.Ví dụ: nếu bạn đang sử dụng `"symfony/symfony": "^3.1"`, có thể có điều gì đó không được chấp nhận trong 3.2 có thể phá vỡ các test ứng dụng của bạn. Hoặc có thể có một lỗi được sửa trong PHP_CodeSniffer và nó sẽ phát hiện các vấn đề về định dạng mới trong code của bạn, điều này có thể dẫn đến cấu trúc code bị hỏng.

Việc cập nhật phụ thuộc nên được cân nhắc, không được tùy tiện. Một trong những lời khuyên dưới đây sẽ nói về điều này chi tiết hơn.

Nghe có vẻ hơi quá, nhưng nó sẽ ngăn các đồng nghiệp của bạn vô tình cập nhật tất cả các phụ thuộc khi thêm một thư viện mới vào project (mà bạn có thể bỏ lỡ trong buổi Code Review).

## Lời khuyên #4: Sử dụng phạm vi phiên bản cho các phụ thuộc thư viện

Nếu bạn đang tạo một thư viện, bạn nên xác định phạm vi phiên bản rộng nhất có thể. Nếu bạn tạo thư viện sử dụng thư viện  `symfony/yaml` để phân tích cú pháp YAML, bạn nên yêu cầu nó như sau:

    
    
    "symfony/yaml": "^3.0 || ^4.0"
    

Điều đó có nghĩa là thư viện của bạn có thể sử dụng `symfony/yaml` từ mọi phiên bản Symfony 3.x hoặc 4.x. Điều quan trọng là vì ràng buộc này sẽ được chuyển đến ứng dụng sử dụng thư viện của bạn.

Trong trường hợp có hai thư viện có yêu cầu xung đột, ví dụ: một cái yêu cầu `~ 3.1.0` và một cái yêu cầu ` ~ 3.2.0`, quá trình cài đặt sẽ thất bại.

## Lời khuyên #5: Bạn nên commit `composer.lock` lên git trong các ứng dụng

Nếu bạn đang tạo _một project_,  bạn chắc chắn muốn commit `composer.lock` lên git. Điều này đảm bảo rằng tất cả mọi người - bạn, đồng nghiệp của bạn, máy chủ CI và máy chủ product của bạn - đang chạy ứng dụng với cùng các phiên bản phụ thuộc.

Thoạt nhìn, nó nghe có vẻ không cần thiết - bạn đã sử dụng một phiên bản cụ thể trong ràng buộc, như đã đề cập trong lời khuyên # 3. Nhưng không, cũng có những phụ thuộc trong các phụ thuộc của bạn mà không bị ràng buộc bởi những ràng buộc này
(ví dụ. `symfony/console` phụ thuộc vào `symfony/polyfill-mbstring`). Vì vậy, nếu không commit `composer.lock`, bạn sẽ không nhận được tập hợp các phụ thuộc.

## Lời khuyên #6: Đặt `composer.lock` vào` .gitignore` trong thư viện

Nếu bạn đang tạo _một thư viện_ (hãy gọi nó là `acme / my-library`), bạn không nên commit file` composer.lock`. Nó [không giúp được gì](https://getcomposer.org/doc/02-libraries.md#lock-file) cho các project đang sử dụng thư viện của bạn.

Hãy tưởng tượng rằng `acme/my-library` sử dụng `monolog/monolog` như là một phụ thuộc. Nếu bạn commit  `composer.lock`, mọi người đang phát triển `acme/my-
library` sẽ sử dụng phiên bản cũ hơn của Monolog. Nhưng khi thư viện hoàn tất, và bạn sử dụng nó trong một project thực, một phiên bản mới hơn của Monolog có thể được cài đặt, và nó có thể không tương thích với thư viện. Nhưng bạn đã không nhận thấy nó trước đây, là vì `composer.lock`!

Tốt nhất là đặt `composer.lock` vào trong `.gitignore` vì vậy bạn sẽ không vô tình commit nó.

Nếu bạn muốn đảm bảo rằng thư viện tương thích với các phiên bản khác nhau của các phụ thuộc của nó, hãy đọc lời khuyên tiếp theo !

## Lời khuyên #7: Chạy các kiến trúc Travis CI với nhiều phiên bản phụ thuộc

> Lời khuyên này chỉ áp dụng cho các thư viện (vì bạn sử dụng phiên bản cụ thể cho các ứng dụng).

Nếu bạn đang xây dựng một thư viện mã nguồn mở, có thể bạn đang sử dụng Travis CI để chạy kiến trúc của nó.

Theo mặc định, Composer cài đặt các phiên bản mới nhất của các phụ thuộc được cho phép bởi các ràng buộc trong `composer.json`. Nó có nghĩa là đối với ràng buộc phụ thuộc `^3.0 || ^4.0`, các kiến trúc sẽ luôn sử dụng phiên bản v4 mới nhất. Vì phiên bản 3.0 chưa bao giờ được kiểm tra, thư viện có thể không tương thích với nó và điều đó sẽ làm cho người dùng của bạn buồn.

May mắn thay, Composer cung cấp một bộ chuyển đổi để cài đặt các phiên bản thấp nhất có thể của các phụ thuộc `--prefer-low` (nên được sử dụng với` --prefer-stable` để ngăn chặn việc cài đặt các phiên bản không ổn định).

Cập nhật cấu hình `.travis.yml` có thể trông giống như sau:

    
    
    language: php
    
    php:
      - 7.1
      - 7.2
    
    env:
      matrix:
        - PREFER_LOWEST="--prefer-lowest --prefer-stable"
        - PREFER_LOWEST=""
    
    before_script:
      - composer update $PREFER_LOWEST
    
    script:
      - composer ci
    

Bạn có thể xem nó cụ thể hơn trong [thư viện mhujer/fio-api-php của tôi](https://github.com/mhujer/fio-
api-php/blob/master/.travis.yml) và [xây dựng matrix trên Travis CI](https://travis-ci.org/mhujer/fio-api-php)

Mặc dù giải pháp này sẽ khắc phục được hầu hết các vấn đề không tương thích, nhưng hãy nhớ rằng có nhiều sự kết hợp trong các phụ thuộc giữa những phiên bản thấp nhất và cao nhất. Và chúng có thể không tương thích với nhau.

## Lời khuyên #8: Sắp xếp các gói khai báo trong require và require-dev theo tên

Cách tốt nhất là sắp xếp các gói trong `require` và `require-dev` theo tên. Nó có thể ngăn chặn việc xung đột không cần thiết khi rebasing một nhánh.
Bởi vì nếu bạn đã thêm một gói vào cuối danh sách trong hai nhánh, sẽ có xung đột khi merge .

Đó là một công việc tẻ nhạt để làm thủ công, vì vậy tốt nhất là  [cấu hình
nó](https://getcomposer.org/doc/06-config.md#sort-packages) trong
`composer.json`:

    
    
    {
    ...
        "config": {
            "sort-packages": true
        },
    â¦
    }
    

Lần tới, bạn `require` một gói mới, nó sẽ được thêm vào một vị trí thích hợp (và không phải đến cuối).

## Lời khuyên #9: Không cố gắng gộp `composer.lock` khi rebase hoặc merge

Nếu bạn thêm một phụ thuộc mới vào `composer.json` (và` composer.lock`) và trước khi nhánh của bạn được sát nhập, có một phụ thuộc khác được thêm vào trong master, bạn cần phải rebase nhánh của bạn. Và bạn sẽ gặp 1 merge-conflict trong `composer.lock`.

Bạn không bao giờ nên cố gắng giải quyết xung đột này theo cách thủ công, bởi vì file `composer.lock` chứa một mã băm của phụ thuộc được định nghĩa trong` composer.json`. Vì vậy ngay cả khi bạn giải quyết được xung đột, kết quả file lock vẫn sẽ không chính xác.

Điều tốt nhất cần làm là tạo `.gitattributes` trong thư mục gốc của project với dòng sau, có nghĩa là git sẽ không cố gắng gộp ` composer.lock`:

    
    
    /composer.lock -merge
    

Bạn có thể khắc phục vấn đề này bằng các sử dụng các nhánh tính năng ngắn hạn được gợi ý trong [Trunk Based Development](https://trunkbaseddevelopment.com/) (bạn nên thực hiện điều này bất cứ giá nào). Khi bạn có một nhánh ngắn hạn,được gộp kịp thời, sẽ tối thiểu rủi ro của việc xung đột khi gộp trong `composer.lock` .Bạn thậm chí có thể tạo một nhánh chỉ để thêm một sự phụ thuộc và gộp nó ngay lập tức.


Nhưng bạn phải làm gì, khi gặp xung đột khi gộp ở `composer.lock` khi
rebase? ải quyết nó với phiên bản từ master, vì vậy bạn sẽ chỉ có những thay đổi trong `composer.json` (gói mới được thêm vào). Và sau đó chạy `composer update --lock`, sẽ cập nhật file` composer.lock` với các thay đổi từ `composer.json`. Bây giờ bạn có thể stage file `composer.lock` được cập nhật và tiếp tục với việc rebase.

## Lời khuyên #10: Hiểu rõ sự khác biệt giữa `require` và `require-dev`

Điều quan trọng là phải biết được sự khác biệt giữa các khối `require` và` require- dev`

Các gói được yêu cầu để chạy ứng dụng hoặc thư viện phải được định nghĩa trong  `require` (e.g. Symfony, Doctrine, Twig, Guzzle). Nếu bạn đang tạo một thư viện, hãy cẩn thận về những gì bạn đặt vào  `require`. Bởi vì mỗi phụ thuộc từ phần này cũng là một phụ thuộc của ứng dụng, sử dụng thư viện.

PCác gói cần thiết cho việc phát triển ứng dụng(hay thư viện) nên được định nghĩa trong require-dev (ví dụ PHPUnit, PHP_codeSniffer, PHPStan).

## Lời khuyên #11: Cập nhật phụ thuộc một cách an toàn
Tôi đoán rằng chúng ta đều đồng ý 1 sự thật là các dependency nên được cập nhật thường xuyên. Thứ tôi muốn nói ở đây là việc cập nhật các dependency nên được thực hiện 1 cách rõ ràng và thận trọng, chứ không phải chỉ là làm cho xong như các việc khác. Nếu bạn tái cấu trúc 1 cái gì đó và tại cùng thời điểm với việc cập nhật 1 vài thư viện, bạn không thể dễ dàng biết là ứng dụng bị hỏng do việc tái cấu trúc hay do việc cập nhật.  

Bạn có thể sử dụng câu lệnh `composer oudated` để xem các dependency có thể  cập nhật. Bạn nên thêm lựa chọn `--direct` (hay `-D`) để chỉ hiển thị danh sách các dependency được khai báo trong `composer.json`. Ngoài ra cũng có lựa chọn `-m` để chỉ hiển thị các bản cập nhật nhỏ.  

**Với mỗi dependency quá hạn thì thực hiện các bước sau đây:**

  1. Tạo một nhánh mới
  2. Cập nhật phiên bản dependency trong `composer.json` lên phiên bản mới nhất.
  3. Chạy lệnh `composer update phpunit/phpunit --with-dependencies` (thay `phpunit/phpunit` bằng thư viện bạn đang update)
  4. Kiểm tra CHANGELOG trong repo thư viện trên Github để xem có sự thay đổi nào hỏng không. Nếu có cập nhật ứng dụng.
  5. Kiểm tra thử ứng dụng trên local (Nếu đang sử dụng Symfony, bạn có thể thấy các cảnh báo phản đối trong Debug Bar)
  6. Commit các thay đổi (`composer.json`, `composer.lock` và bất cứ thứ gì cần thiết để phiên bản mới hoạ động)
  7. Đợi kiến trúc CI hoàn thành
  8. Merge và deploy

Đôi khi sẽ xảy ra vấn đề khi cập nhật nhiều dependency cùng lúc, ví dụ như khi bạn cập nhật Doctrine hay Symfony. Trường hợp này bạn có thể liệt kê tất cả trong câu lệnh update.  


    composer update symfony/symfony symfony/monolog-bundle --with-dependencies
    

Hoặc bạn cũng có thể sử dụng wildcard để cập nhật tất cả các dependency theo 1 namespace cụ thể:  
    
    composer update symfony/* --with-dependencies
    
Tôi biết rằng tất cả những điều này nghe có vẻ thừa thãi, nhưng bạn chắc chắn sẽ cập nhật các dependency 1 cách tình cờ, an toàn hơn vẫn đáng.  

Một các ngắn gọn mà mọi người chấp nhận thực hiện là cập nhật tất cả các dependency `require-dev` cùng lúc (nếu chúng không yêu cầu những thay đổi bên trong code, mặt khác tôi khuyên bạn nên sử dụng các nhánh riêng biệt để dễ dàng review code).  

## Lời khuyên #12:  Bạn có thể định nghĩa các loại dependency khác nhau trong `composer.json`
Ngoài việc định nghĩa các thư viện như là các dependency , bạn cũng có thể định nghĩa các thứ khác ở đây.  
Bạn có thể định nghĩa phiên bản PHP mà ứng dụng/thư viện của bạn hỗ trợ:  
    
    "require": {
        "php": "7.1.* || 7.2.*",
    },
    

Bạn cũng có thể định nghĩa các extensions được yêu cầu trong ứng dụng/thư viện. Điều này rất hữu ích khi bạn cố gắng dockerize ứng dụng của bạn hay khi đồng nghiệp mới của bạn cài đặt ứng dụng lần đầu tiên.  
    
    "require": {
        "ext-mbstring": "*",
        "ext-pdo_mysql": "*",
    },
    

(Bạn nên dùng `*` cho phiên bản của extensions [Chúng có thể không nhất quán](https://getcomposer.org/doc/01-basic-usage.md#platform-packages)).  

## Lời khuyên #13: Xác thực `composer.json` trong quá trình CI build  

`composer.json` và `composer.lock` nên luôn đồng bộ. vì thế, sẽ là ý tưởng hay nếu có kiêm tra tự động. Chỉ cần thêm đoạn sau vào 1 phần của đoạn mã lệnh xây dựng và nó sẽ đảm bảo `composer.lock` đồng bộ với `composer.json`)  
    
    composer validate --no-check-all --strict
    

## Lời khuyên #14: Dùng Composer plugin trong PHPStorm  

Có [composer.json plugin echo PHPStorm](https://plugins.jetbrains.com/plugin/7631-php-composer-json-support). Nó thêm tự động hoàn thiện và xác thực khi thay đổi`composer.json` thủ công.  
Nếu bạn đang dùng ide khác (hay code editor), bạn có thể thiết lập [JSON schema](https://getcomposer.org/schema.json).  

## Lời khuyên #15: Specify the production PHP version in `composer.json`  

Nếu bạn giống tôi và đổi khi [chạy các phiên bản pre-released PHP trên local](https://blog.martinhujer.cz/php-7-2-is-due-in-november-whats-new/), bạn đang gặp vấn đề trong việc cập nhật các dependency lên 1 phiên bản không hoạt động trong sản phẩm. Ngy bây giờ tôi đang sử dụng PHP 7.2.0, có nghĩa là tôi có thể cài đặt các thư viện, mà không hoạt động trên 7.1. Vì sản phẩm đang chạy 7.1, quá trình cài đặt sẽ thất bại.  

Nhưng bạn không cần lo lắng, có cách giải quyết dễ dàng. Chỉ cần định nghĩa phiên bản production PHP trong phần `config` của `composer.jon`  
    
    "config": {
        "platform": {
            "php": "7.1"
        }
    }
    
Đừng nhầm lẫn nó với `require`, nó khác nhau hoàn toàn. Ứng dụng của bạn có thể chạy trên cả 7.1 hay 7.2 và cùng lúc định nghĩa 7.1 như 1 nền tảng (có nghĩa là các dependency luôn cập nhật đến 1 phiên bản tương thích với 7.1) 
    
    "require": {
        "php": "7.1.* || 7.2.*"
    },
    "config": {
        "platform": {
            "php": "7.1"
        }
    },
    

## Lời khuyên #16: Dùng các package riêng tư từ self-hosted Gitlab  

Nên sử dụng `vcs` như là 1 loại repository và Composer sẽ quyết định cách đúng để lấy các package đó. Ví dụ, nếu bạn thêm 1 fork từ Github, nó sẽ sử dụng API của nó để tải file .zip thay vì clone toàn bọ repository.  

Nhưng điều này sẽ phức tạp hợp khi cài đặt riêng tư trên Gitlab. Nếu bạn sử dụng `vcs` như 1 loại repository, Composer sẽ phát hiện ra đây là 1 cài đặt Gitlab cố gắng tải package bằng cách sử dụng API (được yêu cầu API key. Tôi không muốn cài đặt nó, vì vậy tôi cài đặt thiết lập này(sử dụng SSH để clone))    

Trước tiên định nghĩa repository với loại `git`:  

    
    "repositories": [
        {
            "type": "git",
            "url": "git@gitlab.mycompany.cz:package-namespace/package-name.git"
        }
    ]
    

Sau đó dùng package như bạn làm thủ công:  

    
    
    "require": {
        "package-namespace/package-name": "1.0.0"
    }
    

## Lời khuyên #17: Làm sao để tạm thời sử dụng 1 nhánh với bugfix từ fork  

Nếu bạn tìm ra bg trong vài thư viện được public và sủa nó trong bản fork trên github, bạn cần cài thư viện từ repo này thay vì bản official (cho đến khi bugfix được hợp và bản đã sửa được phát hành).  

Ns có thể được thực hiện dễ dàng với [inline aliasing](https://getcomposer.org/doc/articles/aliases.md#require-inline-alias):  

    
    {
        "repositories": [
            {
                "type": "vcs",
                "url": "https://github.com/you/monolog"
            }
        ],
        "require": {
            "symfony/monolog-bundle": "2.0",
            "monolog/monolog": "dev-bugfix as 1.0.x-dev"
        }
    }
    

Bản có thể test fixbug trên local trước khi đẩy bằng cách [dùng `path` như là một kiểu repo](https://getcomposer.org/doc/05-repositories.md#path).

## Update 2018-01-08:
Sau khi phát hành bài viết, tôi nhận được vài gợi ý thêm một vài tip. Vậy chúng đây:

## Lời khuyên #18:  Cài đặt prestissimo để tăng tốc độ cài đặt package

Composer plugin [hirak/prestissimo](https://github.com/hirak/prestissimo) sẽ tăng tốc độ cài dependency bằng cách download song song.  
VÀ điều tốt nhất là? bạn chỉ cần cái 1 lần, trên toàn cục và nó sẽ hoạt độn trên mọi project  
    
    composer global require hirak/prestissimo
    

## Lời khuyên #19:  Kiểm tra ràng buộc phiên bản của bạn nếu cảm thấy không chắc chắn  

Viết đúng ràng buộc phiên bản có thể là 1 việc khó khăn ngay cả sau khi đọc [tài liệu](https://getcomposer.org/doc/articles/versions.md#writing-version-constraints).  

May măn là có [Packagist Semver Checker](https://semver.mwl.be/), bạn có thể kiểm tra phiên bản nào phù hợp với ràng buộc được khai báo. Thay vì chỉ phân tích ràng buộc phiên bản, nó tải dữ liệu từ Packagist để hiển thị phiên bản được realease hiện tại. 

[the result for `symfony/symfony:^3.1`](https://semver.mwl.be/#?package=symfony%2Fsymfony&version=%5E3.1&minimum-
stability=stable).

## Lời khuyên #20: Dùng authoritative class map trong production  

Bạn nên [taoj authoritative class map](https://getcomposer.org/doc/articles/autoloader-optimization.md#optimization-level-2-a-authoritative-class-maps) trong
production. Nó sẽ tăng tốc load class bằng cách thêm moitj thứ trong class-map và bỏ qua kiểm tra filesystem.

bạn có thể thực hiện nó bằng cách chạy lệnh sau như một phần của quá trình xây dựng sản phẩm

    
    composer dump-autoload --classmap-authoritative
    

## Lời khuyên #21: Cấu hình `autoload-dev` để tests  

Bạn thường không muốn thêm các file test trong production class map (cì kích thước file và bộ nhớ). Nó có thể thực hiên bằng cách cấu hình `autoload-dev` (tương tự như `autoload`):  
    
    
    "autoload": {
        "psr-4": {
            "Acme\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Acme\\": "tests/"
        }
    },
    

## Lời khuyên #22: Thử Composer scripts  

Composer scripts là công cụ nhẹ để tạo build script. Tôi đã viêt [bài viết rieeng về chúng](/have-you-tried-composer-scripts/).
