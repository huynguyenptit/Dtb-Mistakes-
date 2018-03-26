## Những lỗi về Database thường gặp của Developers

_Theo nguồn https://stackoverflow.com/questions/621884/database-development-mistakes-made-by-application-developers_

**1. Không sử dụng các chỉ số thích hợp**

Đây có vẻ là một lỗi khá cơ bản nhưng vẫn thường xuyên diễn ra. Các khóa ngoại nên có các chỉ mục trên chúng. Nếu bạn đang sử dụng ~~trong từng trường hợp một~~ (*If you're using a field in a WHERE you should (probably) have an index on it* - **một trường trong lệnh WHERE bạn nên sử dụng chỉ mục trong câu lệnh này**)

~~Vậy đâu là nơi mà bạn nên đánh dấu chỉ mục~~. Những chỉ mục này thường bao gồm những cột dựa trên truy vấn bạn cần thực hiện.

**2. Không thực thi ràng buộc tham chiếu**

Cơ sở dữ liệu của bạn có thể thay đổi nhưng nếu nó hỗ trợ ràng buộc tham chiếu - có nghĩa là tất cả các khoá ngoại được bảo đảm trỏ đến một thực thể tồn tại - bạn nên sử dụng nó.

Dễ dàng có thể thấy được sự thiếu xót trên cơ sở dữ liệu MySQL. Tôi không nghĩ rằng MyISAM hỗ trợ điều này. InnoDB thì lại có. Bạn sẽ thấy được những ai dùng MyISAM hoặc InnoDB nhưng lại không sử dụng nó trong bất kì hoàn cảnh nào.

Xem thêm tại đây:

- [Những khó khăn như NOT NULL và FOREIGN KEY nghiêm trọng như thế nào nếu tôi luôn luôn kiểm soát đầu vào cơ sở dữ liệu của mình bằng PHP](https://stackoverflow.com/questions/382309/how-important-are-constraints-like-not-null-and-foreign-key-if-ill-always-contr)
- [Có phải những khóa ngoại thực sự quan trọng trong thiết kế cơ sở dữ liệu?](https://stackoverflow.com/questions/18717/are-foreign-keys-really-necessary-in-a-database-design)

**3. Sử dụng Natural Primary Keys hay Surrogate Primary Keys**

Khóa Natural là khóa dựa trên những dữ liệu bên ngoài có ý nghĩa và là duy nhất. Một ví dụ cơ bản là mã sản phẩm, mã bưu điện gồm 2 kí tự(US), số an sinh xã hội ..v.v. Surogate hoặc Technical Primary Keys là những điều không có ý nghĩa hoàn toàn bên ngoài hệ thống. Chúng được tạo ra để xác định thực thể, thông thường là các trường auto-incrementing (SQL Server, MySQL, ..v.v) hoặc các trình tự (nhất là Oracle).

Theo quan điểm của tôi, bạn hãy **luôn luôn** sử dụng Surrogate Keys. Vấn đề này đã được đưa ra trong những câu hỏi sau:

- [Bạn thích Primary keys của bạn như thế nào?](https://stackoverflow.com/questions/404040/how-do-you-like-your-primary-keys)
- [Cách thực hành tốt nhất về Primary Key trong các bảng](https://stackoverflow.com/questions/337503/whats-the-best-practice-for-primary-keys-in-tables)
- [Bạn sử dụng định dạng khoá chính nào trong trường hợp này.](https://stackoverflow.com/questions/506164/which-format-of-primary-key-would-you-use-in-this-situation)
- [Surrogate vs. natural/business keys](https://stackoverflow.com/questions/63090/surrogate-vs-natural-business-keys)
- [Liệu tôi có nên một trường dành riêng cho Primary Key?](https://stackoverflow.com/questions/166750/should-i-have-a-dedicated-primary-key-field)

Đó là một vài chủ đề gây tranh cãi, ở đó bạn sẽ không nhận được toàn thể những đồng thuận. Trong khi bạn có thể thấy vài người nghĩ rằng Natural Key là ổn trong một vài trường hợp, bạn sẽ không tìm thấy bất kì sự chê bai nào về Surrogate Keys khác hơn là việc nó được cho là không cần thiết. Nó sẽ là một nhược điểm nhỏ nếu bạn hỏi tôi.

Nhớ rằng, thậm trí [Các quốc gia có thể biến mất](http://en.wikipedia.org/wiki/ISO_3166-1) (ví dụ như, Yugoslavia)

**4. Viết các truy vấn yêu cầu DISTINCT làm việc**

Bạn thường thấy điều này trong các truy vấn được tạo bởi ORM. Nhìn vào những đầu ra log từ Hibernate và bạn sẽ thấy tất cả những truy vấn bắt đầu từ:

SELECT DISTINCT ...

Đây là một cách viết để đảm bảo rằng bạn không phải nhận những dữ liệu trùng lặp (các đối tượng trùng lặp). Đôi lúc bạn sẽ thấy người ta làm điều này khá tốt. Nếu bạn thấy điều này quá nhiều thì thực ra nó chỉ là một cái cờ để đánh dấu. Chứ không phải DISTINCT không tốt hoặc giá trị không hợp lệ
Nó là như vậy nhưng không phải là một **Surrogate** hoặc một **Stopgap** cho việc viết đúng các câu truy vấn.

Nguồn từ [Tại sao tôi ghét DISTINCT](http://weblogs.sqlteam.com/markc/archive/2008/11/11/60752.aspx)

> Theo quan điểm của tôi về nơi mọi thứ bắt đầu trở nên tồi tệ là khi 1 developer đang xây dựng lượng truy vấn đáng kể, join các bảng với nhau và đột nhiên anh ta nhận ra rằng nó **có vẻ** như anh ta đang lấy ra những hàng trùng lặp và ngay lập tức nghĩ ra giải pháp cho vấn đề này thông qua từ khóa DISTINCT và **Đá phăng** tất cả những rắc rối kia.

**5. Khuyến khích tập hợp các kết nối**

Các lỗi thường gặp khác ở các nhà phát triển ứng dụng cơ sở dữ liệu là không nhận ra ~~cái giá của sự kết hơpj (Mệnh đề GROUP BY) có thể được so sánh để join.~~ (**khi đem so sánh với nhau, các câu lệnh kết hợp với nhau sẽ tiêu tốn hơn so với các lệnh join (ví dụ Mệnh đề GROUP BY)**)

Để cho bạn một ý tưởng về sức lan tỏa của nó, Tôi đã viết về chủ đề này rất nhiều lần và rất nhiều đã bị downvote. Ví dụ như:
[Câu lệnh SQL - “join” vs “group by và having”](https://stackoverflow.com/questions/477006/sql-statement-join-vs-group-by-and-having/477013#477013)

> Câu truy vấn thứ nhất:

SELECT useridFROM userrole WHERE roleid IN (1, 2, 3) GROUP by userid HAVING COUNT(1) = 3```

> Thời gian truy vấn: 0.312 s

> Câu truy vấn thứ 2:

SELECT t1.userid FROM userrole t1 JOIN userrole t2 ON t1.userid = t2.userid AND t2.roleid = 2 JOIN userrole t3 ON t2.userid = t3.userid AND t3.roleid = 3 AND t1.roleid = 1

> Thời gian truy vấn: 0.016 s

> Đúng vậy, câu lệnh join mà tôi đề xuất **nhanh hơn gấp ~~12~~(**20**) lần so với câu lệnh tập hợp.**

**6. Không đơn giản hóa các truy vấn phức tạp thông qua các chế độ view**

Không phải tất cả các nhà cung cấp cơ sở dữ liệu cũng hỗ trợ việc xem nhưng phần lớn là có, họ có thể đơn giản hóa các câu truy vấn nếu chúng được sử dụng một cách thận trọng. 
Ví dụ, trong một dự án tôi đã sử dụng [generic Party model](http://www.tdan.com/view-articles/5014/) cho CRM. Đây là một kĩ thuật tạo model đầy mạnh mẽ và linh hoạt nhưng có thể kết nối nhiều join. Trong model này (**gồm**) có:

- **Party**: người và tổ chức;
- **Party Role**: những thứ mà người và tổ chức đóng vai trò, ví dụ như nhân viên và người sử dụng lao động;
- **Party Role Relationship**: làm thế nào những vai trò liên quan đến nhau.

Ví dụ:

- Ted là một Người, đóng một vai trò nhỏ trong Party
- Ted có nhiều vai trò, một trong số đó là Người làm thuê;
- Intel là một tổ chức, đóng một vai trò nhỏ trong Party;
- Intel có nhiều vai trò, một trong số đó là Người quản lí nhân sự;
- Intel thuê Ted, có nghĩa là có một mối quan hệ tương ứng giữa những vai trò đó.

~~Vì~~ (**Nên sẽ**)có 5 bảng được join ~~và~~(**để**) kết nối vs Ted vs nhà tuyển dụng của anh ta. Bạn giả sử tất cả những Người lao động là Người(không phải tổ chức) và cung cấp chế độ trợ giúp:

CREATE VIEW vw_employee AS
SELECT p.title, p.given_names, p.surname, p.date_of_birth, p2.party_name employer_name
FROM person p
JOIN party py ON py.id = p.id
JOIN party_role child ON p.id = child.party_id
JOIN party_role_relationship prr ON child.id = prr.child_id AND prr.type = 'EMPLOYMENT'
JOIN party_role parent ON parent.id = prr.parent_id = parent.id
JOIN party p2 ON parent.party_id = p2.id


Và đột nhiên bạn có một cái nhìn rất đơn giản về dữ liệu mà bạn muốn nhưng trên một mô hình dữ liệu có tính linh hoạt cao.

**7. Không thanh lọc đầu vào**

Đây là một trong những lỗi lớn. Bây giờ tôi thích PHP nhưng nếu bạn không biết bạn đang làm cái gì thì thật dễ để tạo ra một tang web dễ bị tấn công. Không có gì có thể tổng kết tốt hơn so với [story of little Bobby Tables](http://xkcd.com/327/).

Dữ liệu được cung cấp bở người dùng bằng URLs, dữ liệu form **và các Cookie** nên luôn luôn được coi là kẻ thù và cần được thanh lọc. Đảm bảo rằng bạn đang lấy những gì bạn muốn.

**8. Không sử dụng các câu lệnh đã được có sẵn**

Các câu lệnh đã có sẵn là khi bạn biên dịch một truy vấn trừ dữ liệu được sử dụng trong Insert, Update và 
mệnh đề Where và ~~sau đó cung cấp nó~~ (*then supply that later* - **và các phần được thêm vào sau nó**). Ví dụ như:

SELECT * FROM users WHERE username = 'bob'

vs

SELECT * FROM users WHERE username = ?

or

SELECT * FROM users WHERE username = :username

tùy thuộc vào hệ thống của bạn.

Tôi đã nhìn thấy những cơ sở dữ liệu dài đến tận đầu gối của khi họ làm cách này. Về cơ bản, mỗi khi cơ sở dữ liệu hiện đại gặp một truy vấn mới, nó phải biên dịch nó. Nếu nó gặp một truy vấn nó được nhìn thấy trước, bạn đang cho cơ sở dữ liệu cơ hội để cache truy vấn biên dịch và kế hoạch thực hiện. Bằng cách thực hiện các truy vấn rất nhiều bạn đang cho cơ sở dữ liệu cơ hội để trưng bày ra và tối ưu hóa cho phù hợp (ví dụ, bằng cách ghim các truy vấn biên dịch trong bộ nhớ).

Sử dụng các câu lệnh có sẵn cũng sẽ cung cấp cho bạn số liệu thống kê có ý nghĩa về tần suất các truy vấn nhất định được sử dụng.

Các câu lệnh có sẵn cũng sẽ giúp bạn chống lại các cuộc tấn công SQL injection tốt hơn.








