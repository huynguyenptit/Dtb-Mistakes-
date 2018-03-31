**9. Không chuẩn hóa đủ**

[Chuẩn hóa Database](http://en.wikipedia.org/wiki/Database_normalization) là một quá trình cơ bản của việc tối ưu thiết kế Database hoặc cách bạn tổ chức dữ liệu vào các bảng

Chỉ mới tuần này tôi đã lướt qua đoạn code của một ai đó và nó có đoạn implode một mảng, sau đó lưu nó vào 1 trường trong CSDL.

Nps cũng được xuất bản trong [Phương pháp tốt nhất cho việc sắp xếp danh sách ID của User](https://stackoverflow.com/questions/620645/best-method-for-storing-a-list-of-user-ids):

> Tôi đã từng thấy ở một hệ thống khác, các danh sách được lưu trữ trong một mảng tuần tự PHP.

Nhưng sự thiếu chuẩn hóa lại được thể hiện dưới nhiều hình thức.

Xem thêm:

- [Chuẩn hóa: Bao nhiêu là đủ?](http://www.techrepublic.com/article/normalization-how-far-is-far-enough/)
- [SQL by Design: Tại sao bạn cần một CSDL chuẩn hóa? ](http://www.sqlmag.com/Article/ArticleID/4887/sql_server_4887.html)

**10. Chuẩn hóa quá mức**

Điều này có thể xem như một mâu thuẫn với điều ở trên nhưng chuẩn hóa, như những thứ khác, là một công cụ. Đó là một phương tiện để kết thúc và không phải là một kết thúc và của chính nó. Tôi nghĩ nhiều Developer quên điều này và bắt đầu thay đổi một "phương tiện" thành "sự kết thúc". Unit testing là một ví dụ điển hình của điều này

Tôi đã làm việc mỗi tuần 1 lần trên hệ thống mà có độ phân cấp cực lớn cho các Client như sau:

Người được cấp phép - & gt; Nhóm đại lý - & gt; Công ty - & gt; Thực tiễn - & gt; ...
như vậy bạn phải tham gia cùng với khoảng 11 bảng với nhau trước khi bạn có thể có được bất kỳ dữ liệu có ý nghĩa. Đó là một ví dụ điển hình về quá trình bình thường hoá quá mức.

Thêm vào đó, hãy cẩn thận và xem xét việc chuẩn hóa có thể có những lợi ích hiệu năng rất lớn nhưng bạn phải thực sự cẩn thận khi làm điều này.

Xem thêm:

- [Tại sao chuẩn hóa quá mức CSDL lại là một điều xấu](http://www.selikoff.net/blog/2008/11/19/why-too-much-database-normalization-can-be-a-bad-thing/)
- [Chuẩn hóa CSDL bao nhiêu là đủ?](https://stackoverflow.com/questions/496508/how-far-to-take-normalization-in-database-design)
- [Khi nào không cần chuẩn hóa CSDL](http://www.25hoursaday.com/weblog/CommentView.aspx?guid=cc0e740c-a828-4b9d-b244-4ee96e2fad4b)
- [Có lẽ chuẩn hóa lại là điều không bình thương](http://www.codinghorror.com/blog/archives/001152.html)
- [Nguyên do của các cuộc tranh luận về việc chuẩn hóa CSDL nằm ở code](http://highscalability.com/mother-all-database-normalization-debates-coding-horror)


**11. Sử dụng đường cung độc quyền**

Một vòng cung độc quyền là một sai lầm phổ biến nơi một bảng được tạo ra với hai hoặc nhiều Foreign Key nơi mà một và chỉ một trong số họ có thể không null. **Big mistakes** Đối với một điều nó trở nên khó khăn hơn nhiều để duy trì tính toàn vẹn dữ liệu. Sau khi tất cả, ngay cả với tính toàn vẹn tham chiếu, không có gì là ngăn ngừa hai hoặc nhiều hơn các khóa nước ngoài được thiết lập (khó khăn kiểm tra phức tạp).

Từ [Hướng dẫn Thực tiễn về Thiết kế Cơ sở Dữ liệu Quan hệ] (http://books.google.com.au/books?id=7ZAk0YiKQV0C&pg=PA110&lpg=PA110&dq=%22exclusive+arc%22+database&source=bl&ots=AyNPWsac__&sig=gBFIerXckQlVpRdd6ycI5JEgq3U&hl=vi&ei= PzGzSZfrFcPVkAWWyZDZBA & sa = X & oi = book_result & resnum = 1 & ct = kết quả):

> Chúng tôi đã tư vấn mạnh mẽ chống lại việc xây dựng hồ chứa độc quyền bất cứ khi nào có thể, vì lý do tốt mà họ có thể khó viết mã và gây ra nhiều khó khăn về bảo trì.

**12. Không phân tích hiệu xuất của các câu truy vấn**

Chủ nghĩa thực dụng thống trị tối cao, đặc biệt là trong thế giới cơ sở dữ liệu. Nếu bạn đang tuân thủ các quy tắc đến mức chúng trở thành một giáo điều thì bạn rất dễ tạo ra lỗi. Ví dụ về các tổng hợp truy vấn ở phía trên. Các phiên bản tổng hợp trông có vẻ rất "hợp lí" nhưng thực ra chúng lại thật ngu ngốc.Một sự so sánh về hiệu suất đáng nhẽ nên kết thúc cuộc tranh luận(nhưng không) nhưng nhiều hơn thế: đưa ra những tầm nhìn không đáng tin ở nơi đầu tiên thì thật nguy hiểm.

**13. Quá phụ thuộc vào UNION ALL và đặc biệt là cấu trúc UNION**

Một UNION trong SQL đơn giản chỉ là ghép các tập dữ liệu đồng nhất, có nghĩa là chúng có cùng loại và cùng các cột. Điểm khác biệt ở đây là UNION ALL đơn giản chỉ là kết nối và nên được ưu tiên hơn ở bất kì đâu có thể trong khi một UNION sẽ mặc định chạy DISTINCT để loại bỏ các **tuples** trùng lặp.

Các UNION, như là DISTINCT, có địa chỉ của chúng. Có các ứng dụng đầy gía trị. Nhưng nếu bạn thấy mình đang làm rất nhiều, đặc biệt trong các truy vấn phụ, thì có thể bạn đang làm sai. Đó có thể là trường hợp xây dựng truy vấn kém hoặc mô hình dữ liệu được thiết kế kém buộc bạn phải làm những việc như vậy.

Các UNION, cụ thể khi bạn sử dụng trong các kết nối hoặc các truy vấn phụ phụ thuộc, có thể làm tê liệt cơ sở dữ liệu. Cố gắng tránh chúng bất cứ khi nào có thể.

**14. Sử dụng câu lệnh điều kiện OR trong truy vấn**

Chúng có vẻ vô hại. Nhưng sau tất cả, AND lại ổn. OR cũng nên được như vậy phải không? Sai rồi. Về cơ bản một điều kiện AND **hạn chế** dữ liệu cài đặt trong khi một điều kiện OR **kích thích** điều này nhưng không phải là cách để cho phép chúng tối ưu hóa. 
Đặc biệt khi các điều kiện OR khác nhau có thể giao cắt, do đó buộc trình tối ưu hóa có hiệu quả để một hoạt động DISTINCT trong kết quả.

Tồi tệ:

... WHERE a = 2 OR a = 5 OR a = 11

Tốt hơn:

... WHERE a IN (2, 5, 11)

Bây giờ SQL 

Bây giờ, trình tối ưu hoá SQL của bạn có thể biến truy vấn đầu tiên thành thứ hai. Nhưng nó có thể không. Chỉ cần không làm điều đó.

**15. Không thiết kế mô hình dữ liệu của mình để cho phép các giải pháp hiệu suất cao**

Đây là một điều khá khó để định lượng. Chúng có thể được quan sát cụ thể thông qua hiệu quả của chúng. Nếu bạn tìm ra những câu truy vấn phức tạp cho các tác vụ đơn giản hoặc những câu truy vấn để tìm ra các thông tin đơn giản không hiệu quả thì có thể đó là một một hình dữ liệu tồi tệ.

Ở mức độ nào đó thì quan điểm này tóm tắt lại tất cả những điều ở trên nhưng đó cũng là một câu cảnh báo rằng làm những thứ như tối ưu truy vấn thường được xếp thứ nhất nhưng thực tế nó lại nên xếp thứ 2. Đầu tiên và trước hết bạn nên đảm bảo là bạn có một mô hình dữ liệu tốt trước khi cố gắng tối ưu hiệu suất. Như Knuth đã nói:

> Tối ưu hóa sớm là nguồn cơ của mọi rắc rối.

**16. Sử dụng các giao dịch cơ sở dữ liệu không chính xác**

Tất cả dữ liệu thay đổi cho một quá trình cụ thể phải là cốt lõi. Nếu sự thực thi thành công, nó sẽ là đầy đủ. Nếu thất bại, dữ liệu không bị thay đổi. - Không nên có khả năng nào cho việc thay đổi nửa vời.


Lý tưởng nhất, cách đơn giản nhất để đạt được điều này là toàn bộ thiết kế hệ thống nên cố gắng hỗ trợ tất cả các thay đổi dữ liệu thông qua các câu lệnh INSERT / UPDATE / DELETE. Trong trường hợp này, không có xử lý giao dịch đặc biệt là điều cần thiết, vì cơ sở dữ liệu của bạn nên tự động làm như vậy.

Tuy nhiên, nếu bất kì quá trình nào thực sự yêu cầu nhiều câu lệnh được đưa ra như là một khối để giữ dữ liệu trong trạng thái nhất quán, và sau đó kiểm soát các giao dịch là điêu rất quan trọng

- Bắt đầu một giao dịch trước câu lệnh đầu tiên.
- Commit giao dịch sau câu lệnh cuối cùng.
- Trong bất kì hoàn cảnh nào, hãy phục hồi giao dịch. Đừng quên skip/abort tất cả câu lệnh theo lỗi đó.

Cũng nên chú ý cẩn thận đến các subtelties của lớp kết nối cơ sở dữ liệu của bạn, và công cụ cơ sở dữ liệu tương tác trong vấn đề này.

**17. Không hiểu mô hình 'set-based'**

Ngôn ngữ SQL tuân theo một mô hình phù hợp với từng trường hợp cụ thể. Hàng tá các vendor-specific extension là khác nhau, ngôn ngữ này phải vất vả để giải quyết các vấn đề tầm thường trong ngôn ngữ langues như Java, C #, Delphi.

Sự thiếu hiểu biết này thể hiện theo một vài cách.
- Áp dụng quá nhiều thủ tục hay bắt buộc logic trong CSDL
- Việc sử dụng không phù hợp hoặc quá mức của các con trỏ. Đặc biệt là khi một câu truy vấn đơn nhất là đủ.
-Giả định không đúng kích hoạt một lần mỗi hàng bị ảnh hưởng trong cập nhật nhiều hàng.

Xác định phân chia trách nhiệm rõ ràng và cố gắng sử dụng công cụ thích hợp để giải quyết từng vấn đề.