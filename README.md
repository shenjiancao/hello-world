# hello-world
#1.AJAX阶段项目——京东购物车
功能点描述
  (1)异步的用户登录
  (2)异步的显示商品列表，实现分页显示
  (3)异步的添加到“我的购物车”
  (4)查看“我的购物车”
  (5)异步的修改“我的购物车”

所用技术：
  MySQL、PHP、HTTP、AJAX、jQuery、Cookie

实现步骤：   SQL => PHP => HTML/JS			
  (1)编写SQL：jd.sql，数据库名jd，
	创建表：jd_user(uid, uname, upwd) 
	创建表：jd_product(pid, pname, price, pic)	
	创建表：jd_cart( cid,  userId )
	创建表：jd_cart_detail(did, cartId, productId, count)
  (2)编写PHP：data/header.php，包含页头必需的HTML片段
  (3)编写PHP：data/footer.php，包含页尾必需的HTML片段
  (4)编写HTML：productlist.html,待页面加载完成，异步加载页头和页尾。
  (5)编写PHP：user_login.php，接收客户端提交的uname和upwd，执行数据库验证，返回 {"code":1, "uname":"qiangdong", "uid":10} 或 {"code":2, "msg":"用户名或密码错误" }
  (6)修改HTML：productlist.html，默认显示出登录对话框，异步登录验证，失败则提示错误消息，成功则清除掉对话框，显示“欢迎回来：xxxx”
  (7)编写PHP：product_select.php，向客户端输出所有的产品信息，以JSON格式：[{},{},{},....]
  (8)修改HTML: productlist.html，页面加载完后，异步请求产品；分页显示
  (9)编写PHP：cart_product_add.php，接收客户端提交uid、pid，添加入购物车详情表，若已有该商品，则购买数量+1  —— 需要执行多条SQL语句
	SQL1：根据用户编号查询出购物车编号
	SQL2：若没有购物车编号则创建一个购物车，得到购物车编号
	SQL3：根据购物车编号和产品编号查询是否已买过该商品
	SQL4：已购买过则购买数量+1
	SQL5：未购买过则添加购买记录，数量为1
-------------------文华的进度线---------------------
  (10)修改HTML：productlist.html，点击每个商品下“添加到购物车”，异步把uid和pid提交给服务器，实现购物车添加，弹出成功提示消息，提示用户该商品已购物的数量。




2.表单序列化
  $('#formId').serialize( );
  jQuery中提供的表单序列化函数，可以把选定的表单中所有带name属性的输入域连同值转换为k=v形式，全部使用&符号拼接在一起，组成一个大的字符串，用于异步请求数据提交。

3.Web项目中的分页查询 —— 难点 & 重点
  当一个页面需要呈现的数据很多时，不可能一次性全部显示，必须使用分页显示：
  
初始时显示第1页，用户点击某个页号，异步请求对应页中的内容。
分页查询客户端提交的请求消息形如：
  GET /select.php?pageNum=3 HTTP/1.1
分页查询服务器返回的响应消息形如：
  {
	recordCount: 36,	//满足条件的记录的总数
	pageSize: 8,	//页面大小，每页最多显示的记录数
	pageCount: 5, //总的页数
	pageNum: 3,	//当前显示的页号
	data: [ {},{}...{} ]		//当前页中的数据
  }
(1)MySQL如何查询出符合条件的总的记录数量
  SELECT  COUNT(*)  FROM jd_product  WHERE...;
  查询结果集中有一行一列的数据
(2)PHP如何计算页面的总数量：
   ceil( recordCount / pageSize )   //上取整函数
(3)MySQL如何实现查询指定页面中的记录
  提示：不同的数据库实现分页查询的SQL各不相同！
  SELECT * FROM jd_product WHERE ... LIMIT start, count;
  LIMIT: 限制，结果集中从哪一行开始获取数据(从0开始)，最多要多少行。
  第1页： LIMIT 0, 8      01234567
  第2页： LIMIT 8, 8      89101112131415
  第3页： LIMIT 16, 8
  第4页： LIMIT 24, 8
  第5页： LIMIT 32, 8
  第pageNum页： 
		LIMIT (pageNum-1)*pageSize, pageSize

	1  2  3
  1  2  3  4
1 2  3  4  5   
2 3  4  5
3 4  5

4.关系型数据库中两个表间的关系——数据设计理论
  (1)一对一关系
     可以在任一表中添加引用对方表的外键列
	
  (2)一对多关系
	部门vs员工、板块vs帖子、商品vs留言、分类vs商品
	只能在多方表中添加外键列，引用一方的主键
	
  (3)多对多关系
	商品vs购物车、学生vs课程、工人vs车间、员工vs项目
     只能再创建一个中间表，有两个外键列，分别指向每个表的主键
	








