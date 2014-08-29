require 'net/smtp'



class Email_Client



attr_accessor :message_contents, :subject

	def initialize(sender_name, receiver_name, sender_email, receiver_email)
		@sender_name = sender_name
		@receiver_name = receiver_name
		@sender_email = sender_email
		@receiver_email = receiver_email
	end

message_contents = ""
subject = ""

	def send_message
message = <<MESSAGE
From: #{@sender_name} <#{@sender_email}>
To: #{@receiver_name} <#{@receiver_email}>
MIME-Version: 1.0
Content-type: text/html
Subject: #{subject}
		
#{message_contents}
		
MESSAGE
		Net::SMTP.start('smtp.ecarthage.com') do |smtp|
		smtp.send_message message,
		@sender_email, 
		@receiver_email
	end
	
end


	def send_attached_message filename
		filecontent = File.read(filename)
		encodedcontent = [filecontent].pack("m")
		marker = "END OF SECTION"
message = <<MESSAGE
From: #{@sender_name} <#{@sender_email}> 
To: #{@receiver_name} <#{@receiver_email}>
Subject: backup_comparison
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary=#{marker}
--#{marker}
Content-Type: text/plain
Content-Transfer-Encoding:8bit

#{message_contents}
--#{marker}
Content-Type: multipart/mixed; name=\"#{filename}\"
Content-Transfer-Encoding:base64
Content-Disposition: attachment; filename="#{filename}"

#{encodedcontent}
--#{marker}--
MESSAGE
		
		Net::SMTP.start('smtp.ecarthage.com') do |smtp|
		smtp.send_message message,
		@sender_email, 
		@receiver_email
		end
	end
end
