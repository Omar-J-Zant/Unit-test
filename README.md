# Unit-test
create document contain notes of training.


## `TestBed` :


```typescript

fdescribe('create component and testing its functions', () => {
  let service: LoginService;

  let component: AppComponent;
  let fixture: ComponentFixture<AppComponent>;
  
  beforeEach(() => {
    fixture = TestBed.createComponent(AppComponent);
    component = fixture.componentInstance
    
    TestBed.configureTestingModule({
      imports: [HttpClientModule, RouterTestingModule ],
    });
    service = TestBed.inject(LoginService);
  });

  it('should Create AppComponent ', ()=>{
    expect(component).toBeTruthy();
  });

  it('should have title unit-testing ', ()=>{
    expect(component.title).toEqual('unit-testing');
    });
  });
  ```

</br>
</br>

## `Testing Service post` :

--- Auth Service:

```typescript
@Injectable({
  providedIn: 'root',
})
interface Post {
  userId: number;
  id: number;
  title: string;
  body: string;
}
export class AuthService {
  constructor(private http: HttpClient){}
  getPost(postId: number): Observable<Post>
{
  return this.http.get<Post>(`https://jsonplaceholder.typicode.com/posts/${postId}`)
}}
```


</br>
</br>


--- spec file :

> If the spec return `Undefined` ==> the variable send in request is different to the variable in backend . 

```typescript
interface Post {
  userId: number;
  id: number;
  title: string;
  body: string;
}

describe('AithService', ()=>{
  let service: AuthService;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientModule],
    });
    service = TestBed.inject(AuthService);
  });

  it('should get the data successfully', (done:DoneFn) => {
    service.getPost(1).subscribe((post:Post) =>{
      console.log('data is ', post);
      expect(post.id).toEqual(1);
      done();
    })
  })
});
});

```

</br>
</br>

## `Testing Http Mock` :

> What is Mock : It is fake data that the developer writes to ensure that the data it receives is the same as it wants.

--- service File :

```typescript 


```

</br>
</br>

--- spec File :

```typescript 
import {  HttpTestingController, HttpClientTestingModule } from '@angular/common/http/testing';

describe('AuthService with mock data', ()=>{
  let service: AuthService;
  let httpMock : HttpTestingController;  // imported from @angular/common/http/testing

  const postMock = {
    userId: 2,
    id: 1,
    title: "myTitle",
    body: "myBody,"
  }

  beforeEach(() =>{
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule], // imported from @angular/common/http/testing
    });
    service = TestBed.inject(AuthService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('getPost must get data as  expected', () => {
    service.getPost(1).subscribe((data: Post) => {
      console.log('data is' ,data);
      expect(data).toEqual(postMock);
    });
    const req = httpMock.expectOne('https://jsonplaceholder.typicode.com/posts/1');
    req.flush(postMock);
    httpMock.verify();
  });
});

```

> Now if the data in response is diffrent to the form mocked (postMock here), the spec return error.












