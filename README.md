# hosting.in-how-to-enable-and-disable-apache-module
The shell script is written to show how to enable and disable apache  module

The following code is written to enable and disable apache module in HostingRaja server.


#!/usr/bin/env sh

apache_folder_path="/etc/httpd";

status_detail=$1

load_config_file_detail_path="/etc/httpd/conf.modules.d/";

temp_apache_file="/var/log/apache_module"

function check_http

{
	apache_status=`apachectl -t`
  
	#echo $apache_status
  
}

if [ "$#" -ge 2 ]

then 

	ver_d=`rpm -qa \*-release | grep -Ei "oracle|redhat|centos" | cut -d"-" -f3`
  
	#case $status_detail in
  
	case $status_detail in 
  
		enable)
    
			module_detail=$2
      
			so_file_detail=$3
      
			load_config_file_detail=$load_config_file_detail_path$4
      
			r=$(( $RANDOM ))
      
			random_apache_file=$temp_apache_file$r
      
			one_time_change=0
      
      
			module_str="LoadModule $module_detail modules/$so_file_detail"
      
                        grep -rni $module_detail $apache_folder_path >$random_apache_file
                        
			while read module_data_detail;
      
                        do
                        
				#echo $module_data_detail
        
                                trim_module_detail=`echo $module_data_detail | sed -e 's/ / /g' ` 
                                
                                
                                module_file_path=`echo $trim_module_detail | awk '{print $1}' FS=":" `
                                
                                load_module_detail=`echo $trim_module_detail | awk '{print $3}' FS=":"`
                                
                                module_str_first_letter="${load_module_detail:0:1}"
                                
				module_line_no=`echo $trim_module_detail | awk '{print $2}' FS=":"`  
        
				if [[ $module_line_no = *[[:digit:]]* ]]; then
        
        
					sed -i "${module_line_no}d"   $module_file_path;
          
				fi      
                        done < $random_apache_file
                        
			rm -rf $random_apache_file
      
			echo "$module_str" >>$load_config_file_detail
      
			echo "Enable part";
      
			#check_http
      
		;;
		disable)
    
			module_detail=$2
      
			r=$(( $RANDOM  ));
      
                        random_apache_file=$temp_apache_file$r;
                        
			server_restart_status=0
      
			grep -rni $module_detail $apache_folder_path >$random_apache_file
      
			while read module_data_detail;
      
			do
      
				trim_module_detail=`echo $module_data_detail | sed -e 's/ / /g' ` 
        
        
				module_file_path=`echo $trim_module_detail | awk '{print $1}' FS=":" `
        
				load_module_detail=`echo $trim_module_detail | awk '{print $3}' FS=":"`
        
				module_str_first_letter="${load_module_detail:0:1}"
        
				if [ $module_str_first_letter != "#" ]
        
				then 
        
					module_line_no=`echo $trim_module_detail | awk '{print $2}' FS=":"`	
          
					if [[ $module_line_no = *[[:digit:]]* ]]; then
          
						module_str="#"$load_module_detail
            
						server_restart_status=1
            
						sed -i "${module_line_no}a $module_str " $module_file_path;
            
						sed -i "${module_line_no}d"   $module_file_path;
					fi	
          
          
				fi	
        
				#echo $module_file_path;
        
				#echo -e "$load_module_detail\n"
        
			done < $random_apache_file
      
			rm -rf $random_apache_file
      
			check_http
      
			echo "disable part";
      
      
		;;
    
		getmodule)
			file_path=$2
      
			/usr/sbin/httpd -M  >$file_path
      
		;;
    
		*)
    
			echo "Other part";
      
		;;
    
	esac
  
else

	echo "Execute script like this sh scriptfile.sh  status content";
  
	echo "status :- enable or disable";
  
  
fi


So many client complaining about apache ram usage.That is due to unwanted module  installed by apache .
But maximum user using 20% to 30% of  apache module.So rest of the modules are onluy consuming memory .
To disable any of the module,please refer below link which are the module you need which you are not .

https://httpd.apache.org/docs/2.4/mod/
In this shell script we are creating three methods to enable,disable and  getmodule  

-> To enable module we have to execute shell script like this 
            sh scriptpath  enable module_name sofile_name  configuration_file
            Ex:- sh  scriptfile.sh enable proxy_wstunnel_module mod_proxy_wstunnel.so 00-proxy.conf
            
->To disable module we have to execute shell script like this 
            sh scriptpath  disable module_name 
            Ex:- sh  scriptfile.sh disable proxy_wstunnel_module

-> when we execute getmodule case we have to pass argument like this 
           sh scriptpath getmodule tempfilepath
           

           HostingRaja servers are most reliable compared to others. The plans are flexible too. For more details kindly navigate to hostingraja.in


