#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>



/*包含php的sapi的embed头文件*/
#include <sapi/embed/php_embed.h>

#ifdef ZTS
    void ***tsrm_ls;
#endif


int gThreadNum  =   30;

void *processGo(void*arg){
	int i = 0;
	for(i = 0;i <30;i++){
		zend_eval_string("echo 'hello embed world\n';", NULL, "hellworldtest" TSRMLS_CC);
	}
    return NULL;
}

int i=0;
int main(int argc,char** argv){
    
	int argca			= 1;
    char *argva[2]		= { "embedtest", NULL };
    php_embed_init(argca, argva PTSRMLS_CC);

   for(i=0;i< gThreadNum;i++){
        int tid;
        int* num = malloc(sizeof(int));
        *num    = i;
        pthread_create(&tid, NULL, (void *)processGo, num);
    }
    printf("main thread go to sleep \n");
    sleep(10000); 
	php_embed_shutdown(TSRMLS_C);

    return 0;
}
