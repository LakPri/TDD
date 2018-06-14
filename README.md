# TDD

#import <XCTest/XCTest.h>
#import "ViewController.h"
#import "LoginPresenter.h"
#import "LoginView.h"

@class SpyLoginView;

@interface SpyLoginView : NSObject<LoginView>
@property (nonatomic) BOOL isUserNameEmptyErrorShown;
@property (nonatomic) BOOL isPasswordEmptyErrorShown;
@property (nonatomic) BOOL areAllErrorCleared;

@end

@implementation SpyLoginView : NSObject {
}

-(instancetype)init{
    if(self == [super init]){
        _isUserNameEmptyErrorShown = NO;
        _isPasswordEmptyErrorShown = NO;
        _areAllErrorCleared = NO;
    }
    return self;
}

- (void)clealAllErrors {
    _areAllErrorCleared = YES;

}

- (void)showEmptyPasswordError {
    _isPasswordEmptyErrorShown = YES;

}

- (void)showEmptyUserNameError {
    _isUserNameEmptyErrorShown = YES;

}

@end


@interface LoginTests : XCTestCase

@end

@implementation LoginTests

-(void)testShowErrorWhenUserNameisEmpty{
    
    SpyLoginView *loginView = [[SpyLoginView alloc]init];
    LoginPresenter *presenter = [[LoginPresenter alloc]initWithVC:(loginView)];
    
    [presenter validateCredential:@"" andPassword:@"Password"];
    
    XCTAssertTrue(loginView.isUserNameEmptyErrorShown);
}

-(void)testShowErrorWhenPasswordisEmpty{
    
    SpyLoginView *loginView = [[SpyLoginView alloc]init];
    LoginPresenter *presenter = [[LoginPresenter alloc]initWithVC:(loginView)];

    [presenter validateCredential:@"User Name" andPassword:@""];
 
    XCTAssertTrue(loginView.isPasswordEmptyErrorShown);
}


@end


___________________________
@protocol LoginView
@required
-(void)clealAllErrors;
-(void)showEmptyUserNameError;
-(void)showEmptyPasswordError;
@end

______________


#import <Foundation/Foundation.h>
#import "ViewController.h"
#import "LoginView.h"

@interface LoginPresenter : NSObject

@property(nonatomic, strong) id<LoginView> loginDelegate;

-(id)initWithVC:(id<LoginView>)delegate;

-(void)validateCredential:(NSString*)userName andPassword:(NSString*)password;

@end

__________________
#import "LoginPresenter.h"

@implementation LoginPresenter

-(id)initWithVC:(id<LoginView>)delegate{
    if(self = [super init]){
        self.loginDelegate = delegate;
    }
    
    return self;
}

-(void)validateCredential:(NSString*)userName andPassword:(NSString*)password{
    
    [self.loginDelegate clealAllErrors];
    if([userName isEqualToString:@""]){
        [self.loginDelegate showEmptyUserNameError];
    }
    if([password isEqualToString:@""]){
        [self.loginDelegate showEmptyPasswordError];
    }
}

@end

_______________________
#import <UIKit/UIKit.h>
#import "LoginView.h"

@interface ViewController : UIViewController <LoginView>
@property (nonatomic,strong) NSString *calledMethod;
-(void)callDefaultMethod;

@end







#import "ViewController.h"
#import "LoginPresenter.h"

@interface ViewController ()
@property (weak, nonatomic) IBOutlet UITextField *txtUserName;
@property (weak, nonatomic) IBOutlet UITextField *txtPassword;
@property (weak, nonatomic) IBOutlet UILabel *lblUserError;
@property (weak, nonatomic) IBOutlet UILabel *lblPasswordError;
@property (strong, nonatomic) LoginPresenter *loginPresenter;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    self.loginPresenter = [[LoginPresenter alloc]initWithVC:self];
}

- (IBAction)login:(id)sender {
    
    [self.loginPresenter validateCredential:_txtUserName.text andPassword:_txtPassword.text];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


- (void)clealAllErrors {
    self.lblUserError.text = @"";
    self.lblPasswordError.text = @"";
}

- (void)showEmptyPasswordError {
    self.lblPasswordError.text = @"User Name Error";
}

- (void)showEmptyUserNameError {
    self.lblUserError.text = @"Password Error";

}


@end


