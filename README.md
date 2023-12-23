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


.env должен содержаь в себе:

VITE_BACKEND_URL=127.0.0.1:8000

для локала соответственно
