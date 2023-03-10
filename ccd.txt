package weixin;

import java.io.IOException;
import java.io.InputStream;
import java.security.NoSuchProviderException;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.Map;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import javax.sql.DataSource;
import javax.xml.bind.JAXBException;

import kmmag.Connet_xml;
import me.chanjar.weixin.common.api.WxConsts;
import me.chanjar.weixin.common.bean.WxMenu;
import me.chanjar.weixin.common.bean.WxMenu.WxMenuButton;
import me.chanjar.weixin.common.exception.WxErrorException;
import me.chanjar.weixin.common.session.WxSessionManager;
import me.chanjar.weixin.mp.api.WxMpConfigStorage;
import me.chanjar.weixin.mp.api.WxMpMessageHandler;
import me.chanjar.weixin.mp.api.WxMpMessageRouter;
import me.chanjar.weixin.mp.api.WxMpService;
import me.chanjar.weixin.mp.api.WxMpServiceImpl;
import me.chanjar.weixin.mp.bean.WxMpMassNews;
import me.chanjar.weixin.mp.bean.WxMpMassNews.WxMpMassNewsArticle;
import me.chanjar.weixin.mp.bean.WxMpXmlMessage;
import me.chanjar.weixin.mp.bean.WxMpXmlOutMessage;
import me.chanjar.weixin.mp.bean.WxMpXmlOutTextMessage;
import me.chanjar.weixin.mp.bean.result.WxMpUser;

import org.apache.commons.lang3.StringUtils;

import weixin.Log_Record;
import weixin.SendModel;
import weixin.Wx_ini;
import weixin.WxMpServiceInstance;
import weixin.WxMpXMLInMemoryConfigStorage;

import com.alibaba.fastjson.JSONObject;
import com.justep.baas.action.ActionContext;

import common.AEStool;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * 1.需要在xml配置文件前加各公众号的参数才能读取配置,如appid等
 * 2.各个公众号内容在数据库表wx_info中读取,如关注时的内容、关于我们及用户发送的消息回复 3.该类有发送模板消息方法
 * */
public class WxMpServiceInstance {
	protected static final Logger log = LoggerFactory.getLogger(WxMpServiceInstance.class);
	private WxMpService wxMpService;

	public WxMpService getWxMpService() {
		return wxMpService;
	}

	public void setWxMpService(WxMpService wxMpService) {
		this.wxMpService = wxMpService;
	}

	public WxMpConfigStorage getWxMpConfigStorage() {
		return wxMpConfigStorage;
	}

	public void setWxMpConfigStorage(WxMpConfigStorage wxMpConfigStorage) {
		this.wxMpConfigStorage = wxMpConfigStorage;
	}

	public WxMpMessageRouter getWxMpMessageRouter() {
		return wxMpMessageRouter;
	}

	public void setWxMpMessageRouter(WxMpMessageRouter wxMpMessageRouter) {
		this.wxMpMessageRouter = wxMpMessageRouter;
	}

	private WxMpConfigStorage wxMpConfigStorage;
	private WxMpMessageRouter wxMpMessageRouter;
	/**
	 * 创建多个实例常量，以供调用
	 */
	private static WxMpServiceInstance instance = null;
	// instance为AK=goxnj
	private static WxMpServiceInstance instance_goxnj = null;
	// instance为AK=demo
	private static WxMpServiceInstance instance_demo = null;
	// instance为AK=V001
	private static WxMpServiceInstance instance_V001 = null;
	// instance为AK=V002
	private static WxMpServiceInstance instance_V002 = null;
	// instance为AK=V003
	private static WxMpServiceInstance instance_V003 = null;
	// instance为AK=V004
	private static WxMpServiceInstance instance_V004 = null;
	// instance为AK=V005
	private static WxMpServiceInstance instance_V005 = null;
	// instance为AK=V006
	private static WxMpServiceInstance instance_V006 = null;
	// instance为AK=V007
	private static WxMpServiceInstance instance_V007 = null;
	// instance为AK=V008
	private static WxMpServiceInstance instance_V008 = null;
	// instance为AK=V009
	private static WxMpServiceInstance instance_V009 = null;
	// instance为AK=V010
	private static WxMpServiceInstance instance_V010 = null;

	/**
	 * 根据传入不同的参数，实例化各自方法，用于动态生成不同的公众号
	 * 
	 * @param AK
	 * @param flag
	 *            是否新实例化instance,1表示需要,0表示不需要
	 * @return
	 */
	public static WxMpServiceInstance getInstance(String AK, String flag) {
		if (AK.equals("goxnj")) {
			if (instance_goxnj == null || flag.equals("1")) {
				instance_goxnj = new WxMpServiceInstance(AK);
			}
			instance = instance_goxnj;
		} else if (AK.equals("demo")) {
			if (instance_demo == null || flag.equals("1")) {
				instance_demo = new WxMpServiceInstance(AK);
			}
			instance = instance_demo;
		} else if (AK.equals("V001")) {
			if (instance_V001 == null || flag.equals("1")) {
				instance_V001 = new WxMpServiceInstance(AK);
			}
			instance = instance_V001;
		} else if (AK.equals("V002")) {
			if (instance_V002 == null || flag.equals("1")) {
				instance_V002 = new WxMpServiceInstance(AK);
			}
			instance = instance_V002;
		} else if (AK.equals("V003")) {
			if (instance_V003 == null) {
				instance_V003 = new WxMpServiceInstance(AK);
			}
			instance = instance_V003;
		} else if (AK.equals("V004")) {
			if (instance_V004 == null) {
				instance_V004 = new WxMpServiceInstance(AK);
			}
			instance = instance_V004;
		} else if (AK.equals("V005")) {
			if (instance_V005 == null) {
				instance_V005 = new WxMpServiceInstance(AK);
			}
			instance = instance_V005;
		} else if (AK.equals("V006")) {
			if (instance_V006 == null) {
				instance_V006 = new WxMpServiceInstance(AK);
			}
			instance = instance_V006;
		} else if (AK.equals("V007")) {
			if (instance_V007 == null) {
				instance_V007 = new WxMpServiceInstance(AK);
			}
			instance = instance_V007;
		} else if (AK.equals("V008")) {
			if (instance_V008 == null) {
				instance_V008 = new WxMpServiceInstance(AK);
			}
			instance = instance_V008;
		} else if (AK.equals("V009")) {
			if (instance_V009 == null) {
				instance_V009 = new WxMpServiceInstance(AK);
			}
			instance = instance_V009;
		} else if (AK.equals("V010")) {
			if (instance_V010 == null) {
				instance_V010 = new WxMpServiceInstance(AK);
			}
			instance = instance_V010;
		}
		return instance;
	}

	public WxMpServiceInstance(String AK) {
		try {
			// 通过读取本地xml文件方式获取微信配置信息,局限只能读取一个配置文件
			// InputStream inputStream =
			// WxMpServiceInstance.class.getResourceAsStream("weixin.config.xml");
			// WxMpXMLInMemoryConfigStorage config =
			// WxMpXMLInMemoryConfigStorage.fromXml(inputStream);
			// wxMpConfigStorage = config;
			// wxMpService = new WxMpServiceImpl();
			// wxMpService.setWxMpConfigStorage(config);
			// wxMpMessageRouter = new WxMpMessageRouter(wxMpService);
			// 通过数据库表方式获取微信配置信息,支持多用户读取,需要在根目录增加配置文件
			// 获取输入流
	        InputStream inputStream = WxMpServiceInstance.class.getResourceAsStream(AK + "weixin.config.xml");
	        if (inputStream == null) {
	            throw new RuntimeException("输入流为空!");
	        }
	     // 从输入流中加载配置信息
			WxMpXMLInMemoryConfigStorage config = WxMpXMLInMemoryConfigStorage.fromXml(inputStream);
			wxMpConfigStorage = config;
			wxMpService = new WxMpServiceImpl();
			wxMpService.setWxMpConfigStorage(config);
			wxMpMessageRouter = new WxMpMessageRouter(wxMpService);
			System.out.println("appid="+wxMpConfigStorage.getAppId());
			/**
			 * 1a:ak,2a:appid,3a:token,4a:secret,5a:aeskey,6a:appkey,7a:sub,8a:
			 * about,9a:replay
			 **/
			// 读取配置文件中的 appid
	        String appid = "";
	        JSONObject wxini = new JSONObject();
	        try {
	            wxini = Wx_ini.pro(AK, "A");
	            appid = wxini.getString("2a").trim();
	        } catch (Exception e) {
	            throw new RuntimeException("appid读取配置文件失败!");
	        }
	        // 输出日志信息
	        log.info("The appid is {}", appid);
	        log.info("The wxini query is completed in WxMpServiceInstance");
			// System.out.println("WxMpServiceInstance"+appid);
			// this.addTestRouter();
			this.addMenuRouter(wxini);// 重构菜单
			this.addM3_clkRouter(wxini);// 菜单3的M3的点击事件
			this.addM3_1_clkRouter(wxini);// 菜单3的子菜单M3_1的点击事件
			this.addM3_2_clkRouter(wxini);// 菜单3的子菜单M3_2的点击事件
			this.addM3_3_clkRouter(wxini);// 菜单3的子菜单M3_3的点击事件
			this.addM3_4_clkRouter(wxini);// 菜单3的子菜单M3_4的点击事件
			this.addM3_5_clkRouter(wxini);// 菜单3的子菜单M3_5的点击事件
			this.addAboutRouter(wxini);// about关于消息
			this.addbumenRouter(wxini);// 商家入驻
			this.addSubRouter(wxini);// 关注、用户进入、任何消息回复
		} catch (JAXBException e) {
			// throw new RuntimeException(e);
			throw new RuntimeException("wxmp初始化出错!");
		}

	}

	// 菜单点击M3_5_CLK事件回复消息
	/**
	 * 
	 * @param wxini
	 *            动态获取传入的数组
	 * 
	 * @copyright 关于我们的回复
	 */
	private void addM3_5_clkRouter(final JSONObject wxini) {
		// TODO 自动生成的方法存根
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("50a").trim().replace("R", "\n");
				System.out.println("响应M3_5_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_5_CLK").end();
	}

	// 菜单点击M3_4_CLK事件回复消息
	private void addM3_4_clkRouter(final JSONObject wxini) {
		// TODO 自动生成的方法存根
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("48a").trim().replace("R", "\n");
				System.out.println("响应M3_4_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_4_CLK").end();
	}

	// 菜单点击M3_3_CLK事件回复消息
	private void addM3_3_clkRouter(final JSONObject wxini) {
		// TODO 自动生成的方法存根
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("46a").trim().replace("R", "\n");
				System.out.println("响应M3_3_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_3_CLK").end();
	}

	// 菜单点击M3_2_CLK事件回复消息
	private void addM3_2_clkRouter(final JSONObject wxini) {
		// TODO 自动生成的方法存根
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,Map<String, Object> context, WxMpService wxMpService,WxSessionManager sessionManager) throws WxErrorException {
//				System.out.println("addM3_2_clkRouter="+wxini.toJSONString());
				String about = wxini.getString("44a").trim().replace("R", "\n");
				System.out.println("响应M3_2_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT().content("" + about + "").fromUser(wxMessage.getToUserName()).toUser(wxMessage.getFromUserName()).build();
				// //回复图文消息
				// WxMpXmlOutNewsMessage.Item item = new
				// WxMpXmlOutNewsMessage.Item();
				// WxMpXmlOutNewsMessage.Item item1 = new
				// WxMpXmlOutNewsMessage.Item();
				// item.setDescription(about);
				// item.setPicUrl("https://demo.toxnj.com/1.jpg");
				// item.setTitle("关于我们");
				// item.setUrl("http://www.toxnj.com");
				// System.out.println("图文消息");
				// item1.setDescription(about+"1");
				// item1.setPicUrl("https://demo.toxnj.com/1.jpg");
				// item1.setTitle("关于我们");
				// item1.setUrl("http://www.toxnj.com");
				// System.out.println("图文消息1");
				// WxMpXmlOutNewsMessage m = WxMpXmlOutMessage.NEWS()
				// .fromUser(wxMessage.getToUserName())
				// .toUser(wxMessage.getFromUserName())
				// .addArticle(item)//如有多个依次加即可
				// .addArticle(item1)
				// .build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_2_CLK").end();
	}

	// 菜单点击M3_1_CLK事件回复消息
	private void addM3_1_clkRouter(final JSONObject wxini) {
		// TODO 自动生成的方法存根
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("42a").trim().replace("R", "\n");
				System.out.println("响应M3_1_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_1_CLK").end();
	}

	// 拦截内容为test的消息
	private void addTestRouter(JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				System.out.println("响应test指令********************");
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("成功收到测试指令")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		wxMpMessageRouter.rule().async(false).content("test").handler(handler).end();
	}

	// 菜单点击M3_CLK事件回复消息
	private void addM3_clkRouter(final JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("40a").trim().replace("R", "\n");
				System.out.println("响应M3_CLK指令********************" + about);
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为M3_CLK的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("M3_CLK").end();
	}

	// 菜单点击about事件回复消息
	private void addAboutRouter(final JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				String about = wxini.getString("8a").trim().replace("R", "\n");
				System.out.println("响应about指令********************" + about);
				// 纯文字回复
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("" + about + "")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				// //回复图文消息
				// WxMpXmlOutNewsMessage.Item item = new
				// WxMpXmlOutNewsMessage.Item();
				// item.setDescription(about);
				// item.setPicUrl("https://demo.toxnj.com/1.jpg");
				// item.setTitle("关于我们");
				// item.setUrl("http://www.toxnj.com");
				// System.out.println("图文消息");
				// WxMpXmlOutNewsMessage m = WxMpXmlOutMessage.NEWS()
				// .fromUser(wxMessage.getToUserName())
				// .toUser(wxMessage.getFromUserName())
				// .addArticle(item)
				// .build();
				return m;
			}
		};
		// 拦截内容为about的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("about").end();
		// wxMpMessageRouter.rule().async(false).eventKey("about").end();
	}

	// 菜单点击bumen事件回复消息
	private void addbumenRouter(JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				System.out.println("响应bsin指令********************");
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage
						.TEXT()
						.content(
								"请发送您的电话+姓名或者直接联系我们!\r\n咨询电话:028-62509073\r咨询QQ:1278967492")
						.fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为bsin的点击事件消息
		wxMpMessageRouter.rule().async(false).handler(handler).eventKey("bumen").end();
	}

	/**
	 * 关注和任意消息回复,event("subscribe")表示关注时回复消息, wxMessage.getContent()表示获取用户输入的信息
	 * wxMessage.getEvent()表示用户当前的行为,关注为subscribe,点击菜单为view,进入公众号为LOCATION
	 * wxMessage.getEventKey()表示用户点击菜单的值URL
	 */

	private void addSubRouter(final JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,Map<String, Object> context, WxMpService wxMpService,WxSessionManager sessionManager) throws WxErrorException {
				System.out.println("响应Sub指令********************"+ wxMessage.getContent() + wxini.getString("2a"));
				// 获取各个公众号对应的回复内容:sub为关注时回复内容、about为关于我们、replay为客户发送任意消息回复内容
				String sub = wxini.getString("7a").trim().replace("R", "\n");
				String replay = wxini.getString("9a").trim().replace("R", "\n");
				String AK = wxini.getString("1a").trim();
				WxMpXmlOutTextMessage m = null;
				WxMpUser userInfo = wxMpService.userInfo(wxMessage.getFromUserName(), "zh_CN");
				JSONObject result = new JSONObject();// json存参数
				// System.out.println("进入事件"+wxMessage.getMsgType()+"-"+wxMessage.getSendLocationInfo());
				String usernm = wxMessage.getToUserName();
				// 处理用户性别和城市未设置的情况
				String sex = "", city = "";
				if (userInfo.getSex().equals("未知")) {
					sex = "空";
				} else {
					sex = userInfo.getSex();
				}
				if (userInfo.getCity() != null	&& !userInfo.getCity().equals("")) {
					city = userInfo.getCountry() + userInfo.getProvince() + userInfo.getCity();
				} else {
					city = "未设置";
				}
				// 绑定openid,记录登录信息开始
				JSONObject str_parm = new JSONObject();// json存参数
				str_parm.put("lon_wx", wxMessage.getLongitude());
				str_parm.put("lat_wx", wxMessage.getLatitude());
				str_parm.put("openid", userInfo.getOpenId());
				str_parm.put("usernm", userInfo.getNickname());
				str_parm.put("sex", userInfo.getSex());
				str_parm.put("city", userInfo.getCity());
				str_parm.put("appid", wxini.getString("2a").trim());
				str_parm.put("unionid", userInfo.getUnionId());
				if (str_parm.getString("openid").length() > 0) {
					try {
						Log_Record.record(AK, str_parm);//记录进入用户公众号时绑定openid
					} catch (NamingException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					} catch (SQLException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					} finally {

					}
				}
				String SCAN = wxMessage.getEvent();
				if (SCAN == null)	SCAN = "";
				System.out.println("getEvent="+SCAN);
				
				//卡券事件推送过来接收事件,由于该sdk未升级，卡券类相关信息不能获取，只能根据event来判断
				if(SCAN.equals("card_pass_check")){//卡券通过审核
					System.out.println("卡券通过审核");
				}
				if(SCAN.equals("card_not_pass_check")){//卡券未通过审核
					System.out.println("卡券未通过审核");
				}
				if(SCAN.equals("user_view_card")){//进入会员卡事件推送
					System.out.println("进入会员卡");
				}
				if(SCAN.equals("update_member_card")){//会员卡内容更新事件
					System.out.println("会员卡内容更新");
				}
				if(SCAN.equals("user_get_card")){//领取事件推送
					System.out.println("用户领取卡券");
				}
				// 通过扫描生成的二维码进入
				if (SCAN.equals("SCAN")) {
					Qrcode get_str = new Qrcode();// 实例化对象，共享互相传递值
					String uuid = wxMessage.getEventKey();
					System.out.println("getEventKey="+uuid);
					if(uuid.indexOf("last_trade_no") == -1){//判断进入公众号是否为支付后，默认关注公众号，如果不是则执行下面操作
						String in_num = get_str.scstr.getString(uuid);
						if (in_num == null)	in_num = "0";
						long timeStamp = System.currentTimeMillis(); // 获取当前时间戳,也可以是你自已给的一个随机的或是别人给你的时间戳(一定是long型的数据)
						SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");// 这个是你要转成后的时间的格式
						String timap = sdf.format(new Date(Long.parseLong(String.valueOf(timeStamp))));// 时间戳转换成时间
						String params_ok = null;
						try {
							params_ok = AEStool.bcAES(uuid + "&" + timap, "1","apptoxnj999");
						} catch (NoSuchProviderException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						// 加密传入的参数";
						if (Integer.parseInt(in_num) > 0) {
							System.out.println(timap + "扫描有值" + uuid);
							get_str.scstr.put(uuid, -1);// 状态标记为已扫描
							// 超过设定计算则归零从新存储
							if (get_str.j == 20) {
								get_str.ret_openid.clear();
								get_str.j = 1;
							}
							// 将openid写入json
							get_str.ret_openid.put(uuid, userInfo.getOpenId());
							get_str.j++;
						// 重定向
//							String msg = "电脑端正在登录,是否允许?\n<a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
//									+ params_ok
//									+ "&flag=3\">允许登录</a>       <a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
//									+ params_ok + "&flag=4\">阻止登录</a>";
//							m = WxMpXmlOutMessage.TEXT().content("" + msg + "").fromUser(wxMessage.getToUserName()).toUser(wxMessage.getFromUserName()).build();
						}

						// 该二维码已扫描,等待授权
						if (Integer.parseInt(in_num) == -1) {
							String msg = "电脑端正在登录,是否允许?\n<a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok
									+ "&flag=3\">允许登录</a>       <a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok + "&flag=4\">阻止登录</a>";
							m = WxMpXmlOutMessage.TEXT().content("" + msg + "").fromUser(wxMessage.getToUserName()).toUser(wxMessage.getFromUserName()).build();
						}
						// 该二维码已授权,已经失效
						if (Integer.parseInt(in_num) == -2
								|| Integer.parseInt(in_num) == 0) {
							m = WxMpXmlOutMessage.TEXT()
									.content("二维码已失效,请重新获取再扫描!")
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName()).build();
						}
					
					}
				}
				if (wxMessage.getContent() == null&& !wxMessage.getEvent().equals("subscribe")) {// 用户未输入消息,非关注公众号,该行为一般是点击相关菜单或进入公众号
					//记录进入用户公众号时绑定openid
					try {
						Log_Record.record(AK, str_parm);
					} catch (NamingException e1) {
						// TODO Auto-generated catch block
						e1.printStackTrace();
					} catch (SQLException e1) {
						// TODO Auto-generated catch block
						e1.printStackTrace();
					}
					if (wxMessage.getEvent().equals("LOCATION")) {// 进入公众号
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",stampToDate(userInfo.getSubscribeTime() * 1000	+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "LOCATION");
						result.put("dns", "APP");
						result.put("desc_p", "进入公众号");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						System.out.println("进入公众号:"	+ userInfo.getNickname()+ "-"+ userInfo.getSex()+ "-"+ userInfo.getCity()+ "-"+ stampToDate(userInfo.getSubscribeTime()	* 1000 + "") + "-"
								+ wxMessage.getLatitude() + "-"	+ wxMessage.getLongitude());
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getContent() == null	&& wxMessage.getEvent().equals("unsubscribe")) {// getContent和getEvent为取消订阅
						System.out.println("取消订阅");
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						SimpleDateFormat df = new SimpleDateFormat(
								"yyyy-MM-dd HH:mm:ss");// 设置日期格式
						result.put("in_date", df.format(new Date()) + "");// 取消订阅不能获取该用户的关注日期
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "unsubscrib");
						result.put("dns", "APP");
						result.put("desc_p", "取消订阅消息");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("什么都没操作!");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey() == null) {// 群发的消息拦截，否则会报错
						System.out.println("拦截了群发或其它getEventKey() == null的消息!");
						m = WxMpXmlOutMessage.TEXT().content(null).fromUser(wxMessage.getToUserName()).toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey().matches(".*Business%.*")) {
						// System.out.println("Business="+wxMessage.getEventKey());
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了业务通");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了业务通");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey().matches(".*food.*")) {
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了微菜单");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了小财童");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey().matches(
							".*ex_delivery.*")) {
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了快递助手");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了快乐健身");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey().matches(".*process.*")) {
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了施工进度");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了施工进度");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey().matches(".*qrcode.*")) {
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了手机名片");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了手机名片");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					}else if (wxMessage.getEventKey().matches(".*shop.*")) {
						result.put("typ", "0");
						result.put("openid", userInfo.getOpenId());
						result.put("oper_name", userInfo.getNickname());
						result.put("sex", sex);
						result.put("oper_city", city);
						result.put("in_date",
								stampToDate(userInfo.getSubscribeTime() * 1000
										+ ""));
						result.put("lon", wxMessage.getLongitude());
						result.put("lat", wxMessage.getLatitude());
						result.put("type", "VIEW");
						result.put("dns", "APP");
						result.put("desc_p", "点击了线上商城");
						result.put("unionid", userInfo.getUnionId());
						try {
							Connet_xml.getconn(result);// 执行记录
						} catch (NamingException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						} catch (SQLException e) {
							// TODO 自动生成的 catch 块
							e.printStackTrace();
						}
						// System.out.println("点击了手机名片");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					} else if (wxMessage.getEventKey() == null) {// getEventKey()此处需判断，否则发送模板后台会报错,
						System.out.println("推送了模板消息");
						m = WxMpXmlOutMessage.TEXT().content(null)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					}
					// System.out.println(userInfo.getNickname()+"-"+userInfo.getSex()+"-"+userInfo.getCity()+"-"+stampToDate(userInfo.getSubscribeTime()*1000+"")+"-"+wxMessage.getLatitude()+"-"+wxMessage.getLongitude()+"-"+wxMessage.getContent()+"-"+wxMessage.getEvent()+"-"+wxMessage.getEventKey());
				} else if (wxMessage.getContent() != null) {// 用户发送的消息
					result.put("typ", "0");
					result.put("openid", userInfo.getOpenId());
					result.put("oper_name", userInfo.getNickname());
					result.put("sex", sex);
					result.put("oper_city", city);
					result.put(
							"in_date",
							stampToDate(userInfo.getSubscribeTime() * 1000 + ""));
					result.put("lon", wxMessage.getLongitude());
					result.put("lat", wxMessage.getLatitude());
					result.put("type", "SENDMSG");
					result.put("dns", "APP");
					result.put("desc_p", wxMessage.getContent());
					result.put("unionid", userInfo.getUnionId());
					try {
						Connet_xml.getconn(result);// 执行记录
					} catch (NamingException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					} catch (SQLException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					}
					// System.out.println("用户发送消息"+wxMessage.getContent());
					if (wxMessage.getContent().indexOf("@") != -1) {// 用于内部发送数据,提示收到!
						if (AK.equals("demo") || AK.equals("goxnj")) {
							m = WxMpXmlOutMessage.TEXT().content("已收到信息!")
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName())
									.build();
						} else {
							m = WxMpXmlOutMessage.TEXT().content(null)
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName())
									.build();
						}
					} else {// 普通用户发送的消息
						m = WxMpXmlOutMessage.TEXT().content(replay)
								.fromUser(wxMessage.getToUserName())
								.toUser(wxMessage.getFromUserName()).build();
					}
				} else if (wxMessage.getEvent().equals("subscribe")) {// 首次关注公众号
					result.put("typ", "0");
					result.put("openid", userInfo.getOpenId());
					result.put("oper_name", userInfo.getNickname());
					result.put("sex", sex);
					result.put("oper_city", city);
					result.put(	"in_date",stampToDate(userInfo.getSubscribeTime() * 1000 + ""));
					result.put("lon", wxMessage.getLongitude());
					result.put("lat", wxMessage.getLatitude());
					result.put("type", "subscribe");
					result.put("dns", "APP");
					result.put("desc_p", "订阅消息");
					result.put("unionid", userInfo.getUnionId());
					Qrcode get_str = new Qrcode();// 实例化对象，共享互相传递值
					String uuid = wxMessage.getEventKey();
					if(uuid.indexOf("last_trade_no") == -1){//判断进入公众号是否为支付后，默认关注公众号，如果不是则执行下面操作
						String in_num = get_str.scstr.getString(uuid);
						if (in_num == null)
							in_num = "0";
						long timeStamp = System.currentTimeMillis(); // 获取当前时间戳,也可以是你自已给的一个随机的或是别人给你的时间戳(一定是long型的数据)
						SimpleDateFormat sdf = new SimpleDateFormat(
								"yyyy-MM-dd HH:mm:ss");// 这个是你要转成后的时间的格式
						String timap = sdf.format(new Date(Long.parseLong(String
								.valueOf(timeStamp))));// 时间戳转换成时间
						String params_ok = null;
						try {
							params_ok = AEStool.bcAES(uuid + "&" + timap, "1","apptoxnj999");
						} catch (NoSuchProviderException e1) {
							// TODO Auto-generated catch block
							e1.printStackTrace();
						}// 加密传入的参数";
						if (Integer.parseInt(in_num) > 0) {
							System.out.println(timap + "扫描有值" + uuid);
							get_str.scstr.put(uuid, -1);// 状态标记为已扫描
							// 超过设定计算则归零从新存储
							if (get_str.j == 20) {
								get_str.ret_openid.clear();
								get_str.j = 1;
							}
							// 将openid写入json
							get_str.ret_openid.put(uuid, userInfo.getOpenId());
							get_str.j++;
							String msg = "电脑端正在登录,是否允许?\n<a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok
									+ "&flag=3\">允许登录</a>       <a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok + "&flag=4\">阻止登录</a>";
							m = WxMpXmlOutMessage.TEXT().content("" + msg + "")
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName()).build();
						}
						// 该二维码已扫描,等待授权
						if (Integer.parseInt(in_num) == -1) {
							String msg = "电脑端正在登录,是否允许?\n<a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok
									+ "&flag=3\">允许登录</a>       <a href=\"https://app.toxnj.com/baas/weixin/weixin/tmpqrcode?scenewx="
									+ params_ok + "&flag=4\">阻止登录</a>";
							m = WxMpXmlOutMessage.TEXT().content("" + msg + "")
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName()).build();
						}
						// 该二维码已授权,已经失效
						if (Integer.parseInt(in_num) == -2
								|| Integer.parseInt(in_num) == 0) {
							m = WxMpXmlOutMessage.TEXT()
									.content("二维码已失效,请重新获取再扫描!")
									.fromUser(wxMessage.getToUserName())
									.toUser(wxMessage.getFromUserName()).build();
						}
					}
					try {
						Connet_xml.getconn(result);// 执行记录
						Log_Record.record(AK, str_parm);
					} catch (NamingException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					} catch (SQLException e) {
						// TODO 自动生成的 catch 块
						e.printStackTrace();
					}
					//关注公众号，发送的信息
					m = WxMpXmlOutMessage.TEXT().content("" + sub + "")
							.fromUser(wxMessage.getToUserName())
							.toUser(wxMessage.getFromUserName()).build();
				}
				// System.out.println("addSubRouter事件结束");
				return m;
			}
		};
		// wxMpMessageRouter.rule().async(false).event("subscribe").handler(handler).end();//关注时触发
		wxMpMessageRouter.rule().async(false).handler(handler).end();
	}

	// 创建菜单,根据客户判断
	/**
	 * 构建微信菜单,动态获取公众号对应的菜单、名称、URL
	 * 
	 * @param wxini
	 * 
	 *            构建微信菜单,动态获取公众号对应的菜单、名称、URL
	 */
	private void buildMenu(JSONObject wxini) {
		List<WxMenuButton> x5Meuns = new ArrayList<WxMenuButton>();
		/**
		 * AK为各个公众号的ID，每个公众号的菜单不一样，此处对应每个公众号生成不同的菜单 demo为测试的公众号、goxnj为公司公众号
		 */
		String AK = wxini.getString("1a").trim();// 获取到的AK
		String appid = wxini.getString("2a").trim();// 获取appid
		String m1 = wxini.getString("15a").trim();// 获取一级菜单1的名称
		String m1_url = wxini.getString("16a").trim();// 获取一级菜单1的URL
		String m1_1 = wxini.getString("17a").trim();// 获取菜单1的二级菜单1名称
		String m1_1_url = wxini.getString("18a").trim();// 获取菜单1的二级菜单1的URL
		String m1_2 = wxini.getString("19a").trim();// 获取菜单1的二级菜单2名称
		String m1_2_url = wxini.getString("20a").trim();// 获取菜单1的二级菜单2的URL
		String m1_3 = wxini.getString("21a").trim();// 获取菜单1的二级菜单3名称
		String m1_3_url = wxini.getString("22a").trim();// 获取菜单1的二级菜单3的URL
		String m1_4 = wxini.getString("23a").trim();// 获取菜单1的二级菜单4名称
		String m1_4_url = wxini.getString("24a").trim();// 获取菜单1的二级菜单4的URL
		String m1_5 = wxini.getString("25a").trim();// 获取菜单1的二级菜单5名称
		String m1_5_url = wxini.getString("26a").trim();// 获取菜单1的二级菜单5的URL
		String m2 = wxini.getString("27a").trim();// 获取一级菜单2的名称
		String m2_url = wxini.getString("28a").trim();// 获取一级菜单2的URL
		String m2_1 = wxini.getString("29a").trim();// 获取菜单2的二级菜单1名称
		String m2_1_url = wxini.getString("30a").trim();// 获取菜单2的二级菜单1的URL
		String m2_2 = wxini.getString("31a").trim();// 获取菜单2的二级菜单2名称
		String m2_2_url = wxini.getString("32a").trim();// 获取菜单2的二级菜单2的URL
		String m2_3 = wxini.getString("33a").trim();// 获取菜单2的二级菜单3名称
		String m2_3_url = wxini.getString("34a").trim();// 获取菜单2的二级菜单3的URL
		String m2_4 = wxini.getString("35a").trim();// 获取菜单2的二级菜单4名称
		String m2_4_url = wxini.getString("36a").trim();// 获取菜单2的二级菜单4的URL
		String m2_5 = wxini.getString("37a").trim();// 获取菜单2的二级菜单5名称
		String m2_5_url = wxini.getString("38a").trim();// 获取菜单2的二级菜单5的URL
		String m3 = wxini.getString("39a").trim();// 获取一级菜单3的名称
		String m3_url = wxini.getString("40a").trim();// 获取一级菜单3的URL
		String m3_1 = wxini.getString("41a").trim();// 获取菜单3的二级菜单1名称
		String m3_1_url = wxini.getString("42a").trim();// 获取菜单3的二级菜单1的URL
		String m3_2 = wxini.getString("43a").trim();// 获取菜单3的二级菜单2名称
		String m3_2_url = wxini.getString("44a").trim();// 获取菜单3的二级菜单2的URL
		String m3_3 = wxini.getString("45a").trim();// 获取菜单3的二级菜单3名称
		String m3_3_url = wxini.getString("46a").trim();// 获取菜单3的二级菜单3的URL
		String m3_4 = wxini.getString("47a").trim();// 获取菜单3的二级菜单4名称
		String m3_4_url = wxini.getString("48a").trim();// 获取菜单3的二级菜单4的URL
		String m3_5 = wxini.getString("49a").trim();// 获取菜单3的二级菜单5名称
		String m3_5_url = wxini.getString("50a").trim();// 获取菜单3的二级菜单5的URL
		// System.out.println(AK + "进入菜单");
		// 已写死为goxnj，注意修改
		// if(wxini.equals("goxnj")){
		// System.out.println("更新菜单" + wxini);
		// 一级菜单1,业务通
		if (m1.length() > 0) {// 菜单1有设置内容
			System.out.println("m1=" + m1 + "/" + m1_url.indexOf("http") + "/"	+ m1_url.length());
			if (m1_url.length() > 0 && m1_url.indexOf("http") != -1) {// 如果URL有设置内容则无菜单1的二级菜单，直接跳转至URL
				System.out.println("m1_url=" + m1_url);
				WxMenuButton Retail = new WxMenuButton();
				Retail.setName(m1);
				Retail.setType(WxConsts.BUTTON_VIEW);
				if (m1_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
					Retail.setUrl(m1_url);
				} else {
				Retail.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
						+ this.getWxMpConfigStorage().getAppId()
						+ "&redirect_uri="
						+ m1_url
						+ "%3Fappid%3D"
						+ this.getWxMpConfigStorage().getAppId()
						+ "&"
						+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
				}
				x5Meuns.add(Retail);
			} else if (m1_url.length() == 0) {// URL无设置内容，则表示有二级菜单
				WxMenuButton Retail = new WxMenuButton();
				Retail.setName(m1);
				x5Meuns.add(Retail);
				if (m1_url.length() == 0) {// URL无设置内容,有菜单1的二级菜单
					/*
					 * 注意菜单的显示顺序 1 2 3 4 5
					 */
					if (m1_1.length() > 0) {// 二级菜单1有设置内容
						System.out.println("m1_1=" + m1_1);
						// 二级菜单1
						WxMenuButton salespro = new WxMenuButton();
						salespro.setName(m1_1);
						if (m1_1_url.length() > 0	&& m1_1_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单1，直接跳转至URL
							salespro.setType(WxConsts.BUTTON_VIEW);
							if (m1_1_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro.setUrl(m1_1_url);
							} else {
								salespro.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
										+ this.getWxMpConfigStorage().getAppId()
										+ "&redirect_uri="
										+ m1_1_url
										+ "%3Fappid%3D"
										+ this.getWxMpConfigStorage().getAppId()
										+ "&"
										+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail.getSubButtons().add(salespro);
					}

					if (m1_2.length() > 0) {// 二级菜单2有设置内容
						// 二级菜单2
						WxMenuButton salespro2 = new WxMenuButton();
						salespro2.setName(m1_2);
						if (m1_2_url.length() > 0
								&& m1_2_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单2，直接跳转至URL
							salespro2.setType(WxConsts.BUTTON_VIEW);
							if (m1_2_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro2.setUrl(m1_2_url);
							} else {
								salespro2.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m1_2_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail.getSubButtons().add(salespro2);
					}

					if (m1_3.length() > 0) {// 二级菜单3有设置内容
						// 二级菜单3
						WxMenuButton salespro3 = new WxMenuButton();
						salespro3.setName(m1_3);
						if (m1_3_url.length() > 0	&& m1_3_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单3，直接跳转至URL
							salespro3.setType(WxConsts.BUTTON_VIEW);
							if (m1_3_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro3.setUrl(m1_3_url);
							} else {
								salespro3.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m1_3_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail.getSubButtons().add(salespro3);
					}

					if (m1_4.length() > 0) {// 二级菜单4有设置内容
						// 二级菜单4
						WxMenuButton salespro4 = new WxMenuButton();
						salespro4.setName(m1_4);
						if (m1_4_url.length() > 0
								&& m1_4_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单4，直接跳转至URL
							salespro4.setType(WxConsts.BUTTON_VIEW);
							if (m1_4_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro4.setUrl(m1_4_url);
							} else {
								salespro4.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m1_4_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail.getSubButtons().add(salespro4);
					}

					if (m1_5.length() > 0) {// 二级菜单5有设置内容
						// 二级菜单5
						WxMenuButton salespro5 = new WxMenuButton();
						salespro5.setName(m1_5);
						if (m1_5_url.length() > 0
								&& m1_5_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单5，直接跳转至URL
							salespro5.setType(WxConsts.BUTTON_VIEW);
							if (m1_5_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro5.setUrl(m1_5_url);
							} else {
								salespro5.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m1_5_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail.getSubButtons().add(salespro5);
					}
				}
			}
		}

		// 一级菜单2
		if (m2.length() > 0) {// 菜单2有设置内容
			System.out.println("m2=" + m2 + "/" + m2_url.indexOf("http") + "/"	+ m2_url.length());
			if (m2_url.length() > 0 && m2_url.indexOf("http") != -1) {// 如果URL有设置内容则无菜单2的二级菜单，直接跳转至URL
				System.out.println("m2_url=" + m2_url);
				WxMenuButton Retail2 = new WxMenuButton();
				Retail2.setName(m2);
				Retail2.setType(WxConsts.BUTTON_VIEW);
				if (m2_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
					Retail2.setUrl(m2_url);
				} else {
					Retail2.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
							+ this.getWxMpConfigStorage().getAppId()
							+ "&redirect_uri="
							+ m2_url
							+ "%3Fappid%3D"
							+ this.getWxMpConfigStorage().getAppId()
							+ "&"
							+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
				}
				x5Meuns.add(Retail2);
			} else if (m2_url.length() == 0) {// URL无设置内容，则表示有二级菜单
				WxMenuButton Retail2 = new WxMenuButton();
				Retail2.setName(m2);
				x5Meuns.add(Retail2);
				if (m2_url.length() == 0) {// URL无设置内容,有菜单2的二级菜单
					/*
					 * 注意菜单的显示顺序 1 2 3 4 5
					 */
					if (m2_1.length() > 0) {// 二级菜单2有设置内容
						System.out.println("m2_1=" + m2_1);
						// 二级菜单1
						WxMenuButton salespro2 = new WxMenuButton();
						salespro2.setName(m2_1);
						if (m2_1_url.length() > 0
								&& m2_1_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单1，直接跳转至URL
							salespro2.setType(WxConsts.BUTTON_VIEW);
							if (m2_1_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro2.setUrl(m2_1_url);
							} else {
								salespro2.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m2_1_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail2.getSubButtons().add(salespro2);
					}

					if (m2_2.length() > 0) {// 二级菜单2有设置内容
						// 二级菜单2
						WxMenuButton salespro22 = new WxMenuButton();
						salespro22.setName(m2_2);
						if (m1_2_url.length() > 0 && m2_2_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单2，直接跳转至URL
							salespro22.setType(WxConsts.BUTTON_VIEW);
							if (m2_2_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro22.setUrl(m2_2_url);
							} else {
								salespro22.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m2_2_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail2.getSubButtons().add(salespro22);
					}

					if (m2_3.length() > 0) {// 二级菜单3有设置内容
						// 二级菜单3
						WxMenuButton salespro32 = new WxMenuButton();
						salespro32.setName(m2_3);
						if (m2_3_url.length() > 0
								&& m2_3_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单3，直接跳转至URL
							salespro32.setType(WxConsts.BUTTON_VIEW);
							if (m2_3_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro32.setUrl(m2_3_url);
							} else {
								salespro32.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m2_3_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail2.getSubButtons().add(salespro32);
					}

					if (m2_4.length() > 0) {// 二级菜单4有设置内容
						// 二级菜单4
						WxMenuButton salespro42 = new WxMenuButton();
						salespro42.setName(m2_4);
						if (m2_4_url.length() > 0
								&& m2_4_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单4，直接跳转至URL
							salespro42.setType(WxConsts.BUTTON_VIEW);
							if (m2_4_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro42.setUrl(m2_4_url);
							} else {
								salespro42.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m2_4_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail2.getSubButtons().add(salespro42);
					}

					if (m2_5.length() > 0) {// 二级菜单5有设置内容
						// 二级菜单5
						WxMenuButton salespro52 = new WxMenuButton();
						salespro52.setName(m2_5);
						if (m2_5_url.length() > 0
								&& m2_5_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单5，直接跳转至URL
							salespro52.setType(WxConsts.BUTTON_VIEW);
							if (m2_5_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro52.setUrl(m2_5_url);
							} else {
								salespro52.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m2_5_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
						}
						Retail2.getSubButtons().add(salespro52);
					}
				}
			}
		}

		// 一级菜单3,关于我们等回复事件全放在菜单3
		if (m3.length() > 0) {// 菜单3有设置内容
			System.out.println("m3=" + m3 + "/" + m3_url.indexOf("http") + "/"	+ m3_url.length());
			if (m3_url.length() > 0 && m3_url.indexOf("http") != -1) {// 如果URL有设置内容则无菜单3的二级菜单，直接跳转至URL
				System.out.println("m3_url=" + m3_url);
				WxMenuButton Retail3 = new WxMenuButton();
				Retail3.setName(m3);
				Retail3.setType(WxConsts.BUTTON_VIEW);
				if (m3_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
					Retail3.setUrl(m3_url);
				} else {
				Retail3.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
						+ this.getWxMpConfigStorage().getAppId()
						+ "&redirect_uri="
						+ m3_url
						+ "%3Fappid%3D"
						+ this.getWxMpConfigStorage().getAppId()
						+ "&"
						+ "&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
				}
				x5Meuns.add(Retail3);
			} else if (m3_url.length() == 0) {// URL无设置内容，则表示有二级菜单
				WxMenuButton Retail3 = new WxMenuButton();
				Retail3.setName(m3);
				x5Meuns.add(Retail3);
				if (m3_url.length() == 0) {// URL无设置内容,有菜单2的二级菜单
					/*
					 * 注意菜单的显示顺序 1 2 3 4 5
					 */
					if (m3_1.length() > 0) {// 二级菜单2有设置内容
						System.out.println("m3_1=" + m3_1);
						// 二级菜单1
						if (m3_1_url.length() > 0 && m3_1_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单1，直接跳转至URL
							WxMenuButton salespro3 = new WxMenuButton();
							salespro3.setName(m3_1);
							salespro3.setType(WxConsts.BUTTON_VIEW);
							if (m3_1_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro3.setUrl(m3_1_url);
							} else {
								salespro3.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m3_1_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
							Retail3.getSubButtons().add(salespro3);
						} else {// URL有内容，但是无http，表示点击回复事件
							WxMenuButton M3_1_CLK = new WxMenuButton();
							M3_1_CLK.setName(m3_1);
							M3_1_CLK.setType(WxConsts.BUTTON_CLICK);
							M3_1_CLK.setKey("M3_1_CLK");
							Retail3.getSubButtons().add(M3_1_CLK);
						}
					}

					if (m3_2.length() > 0) {// 二级菜单2有设置内容
						// 二级菜单2
						if (m3_2_url.length() > 0 && m3_2_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单2，直接跳转至URL
							WxMenuButton salespro23 = new WxMenuButton();
							salespro23.setName(m3_2);
							salespro23.setType(WxConsts.BUTTON_VIEW);
							if (m3_2_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro23.setUrl(m3_2_url);
							} else {
								salespro23.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m3_2_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
							Retail3.getSubButtons().add(salespro23);
						} else {// URL有内容，但是无http，表示点击回复事件
							WxMenuButton M3_2_CLK = new WxMenuButton();
							M3_2_CLK.setName(m3_2);
							M3_2_CLK.setType(WxConsts.BUTTON_CLICK);
							M3_2_CLK.setKey("M3_2_CLK");
							Retail3.getSubButtons().add(M3_2_CLK);
						}
					}

					if (m3_3.length() > 0) {// 二级菜单3有设置内容
						// 二级菜单3
						if (m3_3_url.length() > 0
								&& m3_3_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单3，直接跳转至URL
							WxMenuButton salespro33 = new WxMenuButton();
							salespro33.setName(m3_3);
							salespro33.setType(WxConsts.BUTTON_VIEW);
							if (m3_3_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro33.setUrl(m3_3_url);
							} else {
								salespro33.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m3_3_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
							Retail3.getSubButtons().add(salespro33);
						} else {// URL有内容，但是无http，表示点击回复事件
							WxMenuButton M3_3_CLK = new WxMenuButton();
							M3_3_CLK.setName(m3_3);
							M3_3_CLK.setType(WxConsts.BUTTON_CLICK);
							M3_3_CLK.setKey("M3_3_CLK");
							Retail3.getSubButtons().add(M3_3_CLK);
						}
					}

					if (m3_4.length() > 0) {// 二级菜单4有设置内容
						// 二级菜单4
						if (m3_4_url.length() > 0	&& m3_4_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单4，直接跳转至URL
							WxMenuButton salespro43 = new WxMenuButton();
							salespro43.setName(m3_4);
							salespro43.setType(WxConsts.BUTTON_VIEW);
							if (m3_4_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro43.setUrl(m3_4_url);
							} else {
								salespro43.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()
												+ "&redirect_uri="
												+ m3_4_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
							Retail3.getSubButtons().add(salespro43);
						} else {// URL有内容，但是无http，表示点击回复事件
							WxMenuButton M3_4_CLK = new WxMenuButton();
							M3_4_CLK.setName(m3_4);
							M3_4_CLK.setType(WxConsts.BUTTON_CLICK);
							M3_4_CLK.setKey("M3_4_CLK");
							Retail3.getSubButtons().add(M3_4_CLK);
						}
					}

					if (m3_5.length() > 0) {// 二级菜单5有设置内容
						// 二级菜单5
						if (m3_5_url.length() > 0	&& m3_5_url.indexOf("http") != -1) {// 如果URL有设置内容则二级菜单5，直接跳转至URL
							WxMenuButton salespro53 = new WxMenuButton();
							salespro53.setName(m3_5);
							salespro53.setType(WxConsts.BUTTON_VIEW);
							if (m3_5_url.indexOf("weixin.qq.com") != -1) {// 该URL是在微信平台新建的图文，不能获取微信授权，直接打开该URL即可
								salespro53.setUrl(m3_5_url);
							} else {
								salespro53.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
												+ this.getWxMpConfigStorage().getAppId()+ "&redirect_uri="
												+ m3_5_url
												+ "%3Fappid%3D"
												+ this.getWxMpConfigStorage().getAppId()
												+ "&"
												+ "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
							}
							Retail3.getSubButtons().add(salespro53);
						} else {// URL有内容，但是无http，表示点击回复事件
							WxMenuButton M3_5_CLK = new WxMenuButton();
							M3_5_CLK.setName(m3_5);
							M3_5_CLK.setType(WxConsts.BUTTON_CLICK);
							M3_5_CLK.setKey("M3_5_CLK");
							Retail3.getSubButtons().add(M3_5_CLK);
						}
					}
				}
			} else {// 一级菜单3URL无http且内容不为空,表示回复点击事件
				WxMenuButton M3_CLK = new WxMenuButton();
				M3_CLK.setName(m3);
				M3_CLK.setType(WxConsts.BUTTON_CLICK);
				M3_CLK.setKey("M3_CLK");
				M3_CLK.getSubButtons().add(M3_CLK);
			}
		}

		// System.out.println("构建菜单完成" + x5Meuns);
		WxMenu x5Menu = new WxMenu();
		x5Menu.setButtons(x5Meuns);
		try {
			wxMpService.menuDelete();
			wxMpService.menuCreate(x5Menu);
		} catch (WxErrorException e) {
			e.printStackTrace();
		}
		// +
		// "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
		// x5Meuns.add(Regst);
		//
		// //一级菜单
		// WxMenuButton More = new WxMenuButton();
		// More.setName("更多功能");
		// x5Meuns.add(More);
		//
		// //二级菜单
		// WxMenuButton process = new WxMenuButton();
		// process.setName("工程进度");
		// process.setType(WxConsts.BUTTON_VIEW);
		// process.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
		// + this.getWxMpConfigStorage().getAppId()
		// +
		// "&redirect_uri=https%3A%2F%2Fdemo.toxnj.com%2Fprocess%2Findex.html%3Fappid%3D"
		// + this.getWxMpConfigStorage().getAppId()+"&"
		// +
		// "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
		// // System.out.println(qrcode.getUrl());
		// More.getSubButtons().add(process);
		//
		// //二级菜单
		// WxMenuButton qrcode = new WxMenuButton();
		// qrcode.setName("手机名片");
		// qrcode.setType(WxConsts.BUTTON_VIEW);
		// qrcode.setUrl("https://open.weixin.qq.com/connect/oauth2/authorize?appid="
		// + this.getWxMpConfigStorage().getAppId()
		// + "&redirect_uri=https%3A%2F%2Fdemo.toxnj.com%2Fqrcode%2Findex.html&"
		// +
		// "response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect");
		// // System.out.println(qrcode.getUrl());
		// More.getSubButtons().add(qrcode);
		//
		// //二级菜单
		// WxMenuButton about = new WxMenuButton();
		// about.setName("关于我们");
		// about.setType(WxConsts.BUTTON_CLICK);
		// about.setKey("about");
		// More.getSubButtons().add(about);
		//
		// //二级菜单
		// WxMenuButton bumen = new WxMenuButton();
		// bumen.setName("商家入驻");
		// bumen.setType(WxConsts.BUTTON_CLICK);
		// bumen.setKey("bumen");
		// More.getSubButtons().add(bumen);
		//
		// WxMenu x5Menu = new WxMenu();
		// x5Menu.setButtons(x5Meuns);
		// try {
		// wxMpService.menuDelete();
		// wxMpService.menuCreate(x5Menu);
		// } catch (WxErrorException e) {
		// e.printStackTrace();
		// }
		// }
	}

	// 接收到menu指令后重构菜单
	private void addMenuRouter(final JSONObject wxini) {
		WxMpMessageHandler handler = new WxMpMessageHandler() {
			public WxMpXmlOutMessage handle(WxMpXmlMessage wxMessage,
					Map<String, Object> context, WxMpService wxMpService,
					WxSessionManager sessionManager) throws WxErrorException {
				System.out.println("响应menu指令********************");
				String AK = wxini.getString("1a").trim();
				JSONObject newwxini = new JSONObject();// 创建新的wx_ini
				try {
					// 获取新的wx_ini
					newwxini = Wx_ini.pro(AK, "A");
				} catch (Exception e) {
					// TODO 自动生成的 catch 块
					e.printStackTrace();
				} finally {
					System.out.println("menu完成wxini查询");
				}
				// 使用新的wx_ini重建菜单
				buildMenu(newwxini);
				// 重新实例化Instance,传入1
				getInstance(AK, "1");
				WxMpXmlOutTextMessage m = WxMpXmlOutMessage.TEXT()
						.content("菜单已更新").fromUser(wxMessage.getToUserName())
						.toUser(wxMessage.getFromUserName()).build();
				return m;
			}
		};
		// 拦截内容为menu的消息
		wxMpMessageRouter.rule().async(false).content("rstmenu").handler(handler).end();
	}

	// 用于微信配置URL时验证的方法
	public void doResponse(HttpServletRequest request,HttpServletResponse response, String AK) throws ServletException,IOException {
		String signature = request.getParameter("signature");
		String nonce = request.getParameter("nonce");
		String timestamp = request.getParameter("timestamp");
		response.setContentType("text/html;charset=utf-8");
		response.setStatus(HttpServletResponse.SC_OK);
		if (!wxMpService.checkSignature(timestamp, nonce, signature)) {
			// 消息签名不正确，说明不是公众平台发过来的消息
			response.getWriter().println("非法请求");
			return;
		}

		String echostr = request.getParameter("echostr");
		if (StringUtils.isNotBlank(echostr)) {
			// 说明是一个仅仅用来验证的请求，回显echostr
			response.getWriter().println(echostr);
			return;
		}

		String encryptType = StringUtils.isBlank(request.getParameter("encrypt_type")) ? "raw" : request.getParameter("encrypt_type");

		WxMpXmlMessage inMessage = null;

		if ("raw".equals(encryptType)) {
			// 明文传输的消息
			inMessage = WxMpXmlMessage.fromXml(request.getInputStream());
		} else if ("aes".equals(encryptType)) {
			// 是aes加密的消息
			String msgSignature = request.getParameter("msg_signature");
			inMessage = WxMpXmlMessage.fromEncryptedXml(request.getInputStream(), wxMpConfigStorage, timestamp,	nonce, msgSignature);
		} else {
			response.getWriter().println("不可识别的加密类型");
			return;
		}

		WxMpXmlOutMessage outMessage = wxMpMessageRouter.route(inMessage);
		if (outMessage != null) {
			if ("raw".equals(encryptType)) {
				response.getWriter().write(outMessage.toXml());
			} else if ("aes".equals(encryptType)) {
				response.getWriter().write(outMessage.toEncryptedXml(wxMpConfigStorage));
			}
			return;
		}
	}

	/**
	 * 将时间戳转换为时间
	 * 
	 * @param s
	 *            时间戳
	 * 
	 * @return String类型
	 */
	public static String stampToDate(String s) {
		String res;
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat(
				"yyyy-MM-dd HH:mm:ss");
		long lt = new Long(s);
		Date date = new Date(lt);
		res = simpleDateFormat.format(date);
		return res;
	}

	/**
	 * 订单推送模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String retun(String openid, String saler, String sheetno,
			String amount, String memo, String group, String sup_name)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), saler, sheetno, amount, memo,
				group, sup_name);
		return code_rtn;
	}

	/**
	 * 施工监控推送模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String process(String openid, String saler, String sheetno,
			String state, String memo, String group, String sup_name)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd_proc(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), saler, sheetno, state, memo,
				group, sup_name);
		System.out.println("进度监控" + accestoken + "-" + code_rtn);
		return code_rtn;
	}

	/**
	 * 新注册账号密码模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String user_reg(String openid, String operno, String psw)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd_user_reg(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), operno, psw);
		System.out.println("新注册账号" + accestoken + "-" + code_rtn);
		return code_rtn;
	}

	/**
	 * 快递到达模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String exp_in(String openid, String sheetno, String shop,
			String addr, String expname, String tel)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException, ParseException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd_ex_in(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), sheetno, shop, addr, expname,
				tel);
		System.out.println("快递达到" + accestoken + "-" + code_rtn);
		return code_rtn;
	}

	/**
	 * 快递签收模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String exp_out(String openid, String sheetno, String shop,
			String tel, String operno, String cpm)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException, ParseException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.ex_out(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), sheetno, shop, tel, operno, cpm);
		System.out.println("快递签收" + accestoken + "-" + code_rtn);
		return code_rtn;
	}

	/**
	 * 给指定openid推送消息
	 * 
	 * @throws IOException
	 * @throws NamingException
	 * @throws SQLException
	 * @throws ClassNotFoundException
	 * 
	 */
	// 给指定openid推送消息
	public String push_info(String openid) throws ClassNotFoundException,
			SQLException, NamingException, IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.push_openid(ret.getString("appid"),
				ret.getString("openid"), ret.getString("access_token"), "");
		System.out.println("openid推送" + accestoken + "-" + code_rtn);
		return code_rtn;
	}

	//
	// /**
	// * 获取用户信息
	// * @param accessToken 接口访问凭证
	// * @param openId 用户标识
	// * @return WeixinUserInfo
	// */
	// public static WeixinUserInfo getUserInfo(String accessToken, String
	// openId) {
	// WeixinUserInfo weixinUserInfo = null;
	// // 拼接请求地址
	// String requestUrl =
	// "https://api.weixin.qq.com/cgi-bin/user/info?access_token=ACCESS_TOKEN&openid=OPENID";
	// requestUrl = requestUrl.replace("ACCESS_TOKEN",
	// accessToken).replace("OPENID", openId);
	// // 获取用户信息
	// JSONObject jsonObject = CommonUtil.httpsRequest(requestUrl, "GET", null);
	//
	// if (null != jsonObject) {
	// try {
	// weixinUserInfo = new WeixinUserInfo();
	// // 用户的标识
	// weixinUserInfo.setOpenId(jsonObject.getString("openid"));
	// // 关注状态（1是关注，0是未关注），未关注时获取不到其余信息
	// weixinUserInfo.setSubscribe(jsonObject.getInt("subscribe"));
	// // 用户关注时间
	// weixinUserInfo.setSubscribeTime(jsonObject.getString("subscribe_time"));
	// // 昵称
	// weixinUserInfo.setNickname(jsonObject.getString("nickname"));
	// // 用户的性别（1是男性，2是女性，0是未知）
	// weixinUserInfo.setSex(jsonObject.getInt("sex"));
	// // 用户所在国家
	// weixinUserInfo.setCountry(jsonObject.getString("country"));
	// // 用户所在省份
	// weixinUserInfo.setProvince(jsonObject.getString("province"));
	// // 用户所在城市
	// weixinUserInfo.setCity(jsonObject.getString("city"));
	// // 用户的语言，简体中文为zh_CN
	// weixinUserInfo.setLanguage(jsonObject.getString("language"));
	// // 用户头像
	// weixinUserInfo.setHeadImgUrl(jsonObject.getString("headimgurl"));
	// } catch (Exception e) {
	// if (0 == weixinUserInfo.getSubscribe()) {
	// log.error("用户{}已取消关注", weixinUserInfo.getOpenId());
	// } else {
	// int errorCode = jsonObject.getInt("errcode");
	// String errorMsg = jsonObject.getString("errmsg");
	// log.error("获取用户信息失败 errcode:{} errmsg:{}", errorCode, errorMsg);
	// }
	// }
	// }
	// return weixinUserInfo;
	// }

	/**
	 * 群发图文消息article 1. thumbMediaId (必填) 图文消息缩略图的media_id，可以在基础支持-上传多媒体文件接口中获得
	 * 2. author 图文消息的作者 3. title (必填) 图文消息的标题 4. contentSourceUrl
	 * 在图文消息页面点击“阅读原文”后的页面链接 5. content (必填) 图文消息页面的内容，支持HTML标签 6. digest
	 * 图文消息的描述 7, showCoverPic 是否显示封面，true为显示，false为不显示
	 * 
	 * @param openId
	 * @return
	 */
	public WxMpMassNewsArticle sendmsg_openid(String openId) {
		JSONObject rerlt = new JSONObject();
		WxMpMassNewsArticle pt = new WxMpMassNews.WxMpMassNewsArticle();
		pt.setThumbMediaId("1");
		pt.setAuthor("业务通");
		pt.setTitle("业务通");
		pt.setContent("测试的具体内容");
		pt.setShowCoverPic(true);
		return pt;
	}

	/**
	 * 给指定openid发送消息
	 * 
	 * @param openId
	 * @param access_token
	 * @param content
	 * @return
	 */
	public JSONObject send_msg(String openId, String content) {
		JSONObject rerlt = new JSONObject();
		String accestoken = null;
		try {
			accestoken = WxMpServiceInstance.getInstance("demo", "0")
					.getWxMpService().getAccessToken();
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		rerlt = SendModel.send_msg("owbQbxIdxdyIUaSivy_wWdpSNh3A", accestoken,
				"测试内容噢!\r\n积分变动:\r\n当前积分20\r\n累计积分500");
		return rerlt;
	}
	

	/**
	 * YJK供应商商品订单模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String send_supno(String openid, String sup_name, String sup_tel,String sheet_no)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd_supno(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), sup_name, sup_tel,sheet_no);
		return code_rtn;
	}

	/**
	 * YJK采购部商品订单模板消息方法
	 * 
	 * @param openid
	 *            用户的微信号
	 * 
	 * @return String类型
	 * 
	 * @return String类型
	 */

	public String send_cslno(String openid, String oper_name, String oper_tel)
			throws ClassNotFoundException, SQLException, NamingException,
			IOException {
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		try {// 获取access_token
			String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
			accestoken = WxMpServiceInstance.getInstance(AK, "0")
					.getWxMpService().getAccessToken();
			// accestoken =
			// UserInfo.instance.getWxMpService().getAccessToken();//弃用通过userinfo获取accestoken,通过WxMpServiceInstance动态获取
		} catch (WxErrorException e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		} catch (Exception e) {
			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		ret.put("appid", appid);
		ret.put("openid", openid);
		ret.put("secret", secret);
		ret.put("access_token", accestoken);
		String code_rtn = SendModel.sendmd_clsno(ret.getString("appid"),
				ret.getString("openid"), ret.getString("secret"),
				ret.getString("access_token"), oper_name, oper_tel);
		return code_rtn;
	}
	
	/*
	 * 根据传人ak和openid,获取微信token相关信息，用于发送消息模版
	 */
	public JSONObject get_infos(String appid_in) throws Exception{
		String appid = this.getWxMpConfigStorage().getAppId();
		String secret = this.getWxMpConfigStorage().getSecret();
		JSONObject ret = new JSONObject();
		String accestoken = null;
		String AK = Wx_ini.pro(appid, "B").getString("1a").trim();// 获取配置文件的AK
		accestoken = WxMpServiceInstance.getInstance(AK, "0").getWxMpService().getAccessToken();
		ret.put("AK", AK);
		ret.put("secret", secret);
		ret.put("accestoken", accestoken);
		return ret;
	}
}
