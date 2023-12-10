запустить quasar build, скопировать содержимое папки dist/spa/* в /data/www

вот конфиг nginx:

http {

    include mime.types;
    
    server {
    
    index index.html;
    
     	    location / {
          
	        try_files $uri $uri/ index.html;
         
	    	  root /data/www;
        
		     }
       
	  }
   }
