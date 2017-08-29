### һ�����½ڽ����Ƕ�`angular4`��Ŀ���������������װ��`model`��
> ����������Ŀ[angular4��Ŀ����ṹ](https://github.com/kuangshp/angular2-prop)�Ļ�������չ��,����`javaweb`��Ŀ�����ֲ���˵����`service`���ܻ�õ�,����Ϊ�˲���`angular4`�б�����еķ������ͻ,����ȡ��Ϊ`model`�����ݲ�򽻵���

### ������Ŀ�ṹ
![����дͼƬ����](http://img.blog.csdn.net/20170829093427466?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva3VhbmdzaHAxMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### �������½�ʹ�õ��ļ�����

* 1����װ�˴�ͷ����Ϣ��(���ܳ��ڰ�ȫ����`CSRF`�������,����`python-web`�����ͱ���Ҫ��`tockend`ͷ��Ϣ)
* 2���򵥵ķ�װ��`get`��`post`����Ļ���ģ��
* 3���ѻ���ģ�͵���ע�뵽��Ŀ�����еĽӿ�ģ����
* 4��`form`���ύ���ݽ��������õ�ģ��ֲ������ķ�ʽ��ȡ������ֵ
* 5��ʹ�ô��������������п��������
* 6��ʹ��`express`�һ����˷�����
* 7��`node`��������`mySQL`���ݿ�Ĳ�ѯ����������

### �ġ���Ҫ��������
* 1������ģ�ʹ���ķ�װ

    ```javascript
    import { Injectable } from '@angular/core';
    import { Http, Headers } from '@angular/http';
    import { Observable } from 'rxjs/Observable';
    import 'rxjs/Rx';
    
    @Injectable()
    export class ServiceBaseService {
        constructor(private http: Http) { }
        /**
         * @param {string} url��ַ
         * @param {any} [options]��ѡ�ύ�Ĳ���
         * @param {any} [header]��ѡ���õ�ͷ��Ϣ
         * @memberof ServiceBaseService
         * @title: ��װһ��get����Ļ�����
         */
        getData(url: string, options?: any, myheaders?: any): Observable<any> {
            // ��������ͷ
            const myHeaders: Headers = new Headers();
            // tslint:disable-next-line:forin
            for (const key in myheaders) {
                myHeaders.append(key, myheaders[key]);
            };
            url += (url.indexOf('?') < 0 ? '?' : '&') + this.param(options);
            return this.http.get(url, { headers: myHeaders }).map(res => res.json());
        }
    
        /**
         * @param url��ַ
         * @param options�ύ������
         * @param myheaders��ѡ��������ͷ
         * @title:��װһ��post�������ݵ�
         */
        postData(url: string, options: any, myheaders?: any): Observable<any> {
            const myHeaders: Headers = new Headers();
            myHeaders.append('Content-Type', 'application/json');
            // tslint:disable-next-line:forin
            for (const key in myheaders) {
                myHeaders.append(key, myheaders[key]);
            };
            return this.http.post(url, options, { headers: myHeaders });
        }
        /**
         * @param {any} data
         * @returns
         * @memberof ServiceBaseService
         * @title:��װһ�����л�get����Ĳ����ķ���
         */
        param(data): string {
            let url = '';
            // tslint:disable-next-line:forin
            for (const k in data) {
                const value = data[k] !== undefined ? data[k] : '';
                url += `&${k}=${encodeURIComponent(value)}`;
            }
            return url ? url.substring(1) : '';
        }
    }
    ```
    
* 2����Ŀ�ĸ�Ŀ¼�д���`proxy.conf.json`�����������

    ```json
    {
        "/wiseoper/**": {
            "target": "http://localhost:3000",
            "secure": false
        }
    }
    ```
    
* 3�����ڴ����������ע���[��ο�](http://blog.csdn.net/kuangshp128/article/details/71909331)
* 4������`get`�����ȡ���ݵ�ģ��

    ```javascript
    import { Injectable } from '@angular/core';
    import { ServiceBaseService } from 'app/model/service-base.service';
    
    @Injectable()
    export class GetServiceService {
    
        constructor(private serviceBase: ServiceBaseService) { }
        // ��ȡ����  
        getData() {
            const url = '/wiseoper/';
            return this.serviceBase.getData(url, {name: 'hello', age: 20}, {QQ: '332904234'});
        }
    }
    ```
    
* 5���������`component.ts`��ֱ�ӵ���ģ���еķ����Ϳ���

    ```javascript
    // ֱ�ӵ���get�������ݵķ���
    getDate(): void {
        this.getService.getData().subscribe(data => {
            console.log(data);
        });
    }
    ```
    
* 6�����Դӿ���̨�ϲ鿴������ͷ��ӵ�����

### �塢����`post`���ʵķ�װ
* 1������������

    ```javascript
    router.post("/wiseoper/register",(req,res)=>{
        //�����post�ύ���ݾ���req.body����  
        console.log(req.body);
        db("insert into cms_user(username,password) values(?,?)",[req.body.username,req.body.password],(err,data)=>{
            console.log(err);
            if (err){
                res.json({
                    code:0,
                    info:"ע��ʧ��"
                });
            }
            console.log(data);
            if (data){
                res.json({
                    code:1,
                    info:"ע��ɹ�"
                });
            }
        })
    });
    ```
    
* 2��`angular4`�ж�`post`����ķ�װ

    ```javascript
    /**
     * @param url��ַ
     * @param options�ύ������
     * @param myheaders��ѡ��������ͷ
     * @title:��װһ��post�������ݵ�
     */
    postData(url: string, options: any, myheaders?: any): Observable<any> {
        const myHeaders: Headers = new Headers();
        myHeaders.append('Content-Type', 'application/json');
        // tslint:disable-next-line:forin
        for (const key in myheaders) {
            myHeaders.append(key, myheaders[key]);
        };
        return this.http.post(url, options, { headers: myHeaders });
    }
    ```
