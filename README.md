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
