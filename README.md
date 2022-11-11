# Unit-test
create document contain notes of training.


[https://content.web-repository.com/s/708629485174503921/uploads/Slides/Unit_Testing-1023769.pdf](Link) .

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
    req.flush(postMock);  // make the response equal to postMock .
    httpMock.verify();   
  });
});

```

> Now if the data in response is diffrent to the form mocked (postMock here), the spec return error.

</br>
</br>

### There are 3 way to async the spec test :

--- This code is same to all three code below:


>The async method still feels like we are working async code. So, Angular team provides another utility that make us feel we are writing synchronous code. This utility is fakeAsync. The use of fakeAsync is shown below. Note, that we used `tick` utility function as well. This is also Angular testing utility function. It simulates passage of time until all pending asynchronous activities finish. So, after dispatching the event we called `tick()` to wait for Angular to handle the event and take necessary action.



```typescript
import { TestBed, async, whenStable, fakeAsync, tick, ComponentFixture } from '@angular/core/testing';
import { LoginComponent } from './login.component';
import { AuthService } from "./auth.service";
import { DebugElement } from "@angular/core";
import { By } from "@angular/platform-browser";

describe('Component: Login', () => {

    let component: LoginComponent;
    let fixture: ComponentFixture<LoginComponent>;
    let authService: AuthService;
    let el: DebugElement;

    beforeEach(() => {

        // refine the test module by declaring the test component
        TestBed.configureTestingModule({
            declarations: [LoginComponent],
            providers: [AuthService]
        });

        // create component and test fixture
        fixture = TestBed.createComponent(LoginComponent);

        // get test component from the fixture
        component = fixture.componentInstance;

        // UserService provided to the TestBed
        authService = TestBed.get(AuthService);

        //  get the "a" element by CSS selector (e.g., by class name)
        el = fixture.debugElement.query(By.css('a'));
    });
```

1- `Using fakeAsync()` :

--- spec File :

```typescript
    it('Button label via fakeAsync() and tick()', fakeAsync(() => {
        expect(el.nativeElement.textContent.trim()).toBe('');
        fixture.detectChanges();
        expect(el.nativeElement.textContent.trim()).toBe('Login');

        spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));

        component.ngOnInit();
        // Simulates the passage of time until all pending asynchronous activities complete
        tick();
        fixture.detectChanges();
        expect(el.nativeElement.textContent.trim()).toBe('Logout');
    }));
```

</br>
</br>

2- `Using async() ` :

--- Spec File :

```typescript
    it('Button label via async() and whenStable()', async(() => {
        // async() knows about all the pending promises defined in it's function body.
        fixture.detectChanges();
        expect(el.nativeElement.textContent.trim()).toBe('Login');
        spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));

        fixture.whenStable().then(() => {
            // This is called when ALL pending promises have been resolved
            fixture.detectChanges();
            expect(el.nativeElement.textContent.trim()).toBe('Logout');
        });

        component.ngOnInit();

    }));
```

</br>
</br>

3- `Using Done()` :

--- spec File :

```typescript
    it('Button label via jasmine.done', (done) => {
        fixture.detectChanges();
        expect(el.nativeElement.textContent.trim()).toBe('Login');

        // Make the authService return a promise that resolves to true
        let spy = spyOn(authService, 'isAuthenticated').and.returnValue(Promise.resolve(true));
        // We trigger the component to check the authService again
        component.ngOnInit();

        // We now want to call a function when the Promise returned from authService.isAuthenticated() is resolved
        spy.calls.mostRecent().returnValue.then(() => {
            // The needsChanged boolean has been updated on the Component so to update the template we trigger change detection
            fixture.detectChanges();
            // Now the label is Logout
            expect(el.nativeElement.textContent.trim()).toBe('Logout');
            // We tell jasmine we are done with this test spec
            done();
        });
    });
```


</br> 
</br>

```typescript
// Simulates the asynchronous passage of time

tick(1);
```


</br>
</br>


> Note::::   Can't bind to 'ngModel' since it isn't a known property of 'input' 
> this messsage is shown when you testing an input has ngModel, so you want to import `FormsModule` library from `@angular/forms` .








