###Android 后台发送邮件
jar地址：[mail三件套](http://yunpan.cn/cuj3r8xgUVSzt) （提取码：a210）

demo地址: [EmailSenderDemo](http://yunpan.cn/cuj38ISwAy5hp) （提取码：68bc）
####用到的权限
  <uses-permission android:name="android.permission.SEND_SMS" />
   
  <uses-permission android:name="android.permission.INTERNET" />    
   
   
####使用代码


            new Thread(new Runnable() {                   
                  @Override
                    public void run() {
                        try {
                            EmailSender sender = new EmailSender();
                            //服务器地址，端口号
                            sender.setProperties("smtp.126.com", "25");
                            sender.setMessage("1xxxxxxxx@126.com", "android:", "测试");
                            sender.setReceiver(new String[]{"xxxxxxxx@126.com"});
                            //服务器地址，账号，密码                       
                            sender.sendEmail("smtp.126.com", "2xxxxxxxx@126.com", "xxxxxx");
                            Looper.prepare();
                            Toast.makeText(MainActivity.this,"发送成功",Toast.LENGTH_LONG).show();
                            Looper.loop();
                       } catch (AddressException e) {
                            // TODO Auto-generated catch 
                           block
                           e.printStackTrace();
                           Looper.prepare();
                            
                            Toast.makeText(MainActivity.this,"发送失
                          败"+e,Toast.LENGTH_LONG).show();
                            
                            Log.e("a","发送失败"+e);
                            
                            Looper.loop();
                            
                        } catch (MessagingException e) {
                            // TODO Auto-generated catch block
                            e.printStackTrace();
                            Looper.prepare();
                            Toast.makeText(MainActivity.this,"发送失败"+e,Toast.LENGTH_LONG).show();
                            Log.e("a", "发送失败" + e);
                            Looper.loop();
                        }
                    }
                }).start();
                
  注:1xxxxxxxx@126.com和2xxxxxxxx@126.com为同一个地址
  
#### 核心功能类EmailSender
	
	
		package com.example.www1.emailsenderdemo;

		import java.io.File;
		import java.util.Date;
		import java.util.Properties;
		import javax.activation.DataHandler;
		import javax.activation.FileDataSource;
		import javax.mail.Address;
		import javax.mail.Message;
		import javax.mail.MessagingException;
		import javax.mail.Session;
		import javax.mail.Transport;
		import javax.mail.internet.AddressException;
		import javax.mail.internet.InternetAddress;
		import javax.mail.internet.MimeBodyPart;
		import javax.mail.internet.MimeMessage;
		import javax.mail.internet.MimeMultipart;


		public class EmailSender {
			private Properties properties;
			private Session session;
			private Message message;
			private MimeMultipart multipart;
		public EmailSender() {
			super();
			this.properties = new Properties();
		}
		public void setProperties(String host,String post){
			this.properties.put("mail.smtp.host",host);
			this.properties.put("mail.smtp.post",post);
			this.properties.put("mail.smtp.auth",true);
			this.session= Session.getInstance(properties);
			this.message = new MimeMessage(session);
			this.multipart = new MimeMultipart("mixed");
		}
		/**
		 *
		 * @param receiver  收件地址
		 * @throws MessagingException
		 */
		public void setReceiver(String[] receiver) throws MessagingException {
			Address[] address = new InternetAddress[receiver.length];
			for(int i=0;i<receiver.length;i++){
				address[i] = new InternetAddress(receiver[i]);
			}
			this.message.setRecipients(Message.RecipientType.TO, address);
		}
	
		/**
		 *
		 * @param from	来自邮箱地址
		 * @param title  标题
		 * @param content  内容
		 * @throws AddressException
		 * @throws MessagingException
		 */
		public void setMessage(String from,String title,String content) throws AddressException, MessagingException {
			this.message.setFrom(new InternetAddress(from));
			this.message.setSubject(title);
			MimeBodyPart textBody = new MimeBodyPart();
			textBody.setContent(content,"text/html;charset=gbk");
			this.multipart.addBodyPart(textBody);
		}
	
		/**
		 *
		 * @param filePath
		 * @throws MessagingException
		 */
		public void addAttachment(String filePath) throws MessagingException {
			FileDataSource fileDataSource = new FileDataSource(new File(filePath));
			DataHandler dataHandler = new DataHandler(fileDataSource);
			MimeBodyPart mimeBodyPart = new MimeBodyPart();
			mimeBodyPart.setDataHandler(dataHandler);
			mimeBodyPart.setFileName(fileDataSource.getName());
			this.multipart.addBodyPart(mimeBodyPart);
		}
	
		/**
		 *
		 * @param host
		 * @param account
		 * @param pwd
		 * @throws MessagingException
		 */
		public void sendEmail(String host,String account,String pwd) throws MessagingException {
	
			this.message.setSentDate(new Date());
			this.message.setContent(this.multipart);
			this.message.saveChanges();
			Transport transport=session.getTransport("smtp");
			transport.connect(host,account,pwd);
			transport.sendMessage(message, message.getAllRecipients());
			transport.close();
		}			
	}
