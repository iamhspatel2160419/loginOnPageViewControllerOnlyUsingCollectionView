# loginOnPageViewControllerOnlyUsingCollectionView

(1) In this tutorial constraints are given using Anchor NSlayou
       The view have given Extension and creating methods for it. 
       
         import UIKit
         extension UIView {

            func anchorToTop(_ top: NSLayoutYAxisAnchor? = nil, left: NSLayoutXAxisAnchor? = nil, bottom: NSLayoutYAxisAnchor? = nil, right: NSLayoutXAxisAnchor? = nil) {

                anchorWithConstantsToTop(top, left: left, bottom: bottom, right: right, topConstant: 0, leftConstant: 0, bottomConstant: 0, rightConstant: 0)
            }

            func anchorWithConstantsToTop(_ top: NSLayoutYAxisAnchor? = nil, left: NSLayoutXAxisAnchor? = nil, bottom: NSLayoutYAxisAnchor? = nil, right: NSLayoutXAxisAnchor? = nil, topConstant: CGFloat = 0, leftConstant: CGFloat = 0, bottomConstant: CGFloat = 0, rightConstant: CGFloat = 0) {

                _ = anchor(top, left: left, bottom: bottom, right: right, topConstant: topConstant, leftConstant: leftConstant, bottomConstant: bottomConstant, rightConstant: rightConstant)
            }

            func anchor(_ top: NSLayoutYAxisAnchor? = nil, left: NSLayoutXAxisAnchor? = nil, bottom: NSLayoutYAxisAnchor? = nil, right: NSLayoutXAxisAnchor? = nil, topConstant: CGFloat = 0, leftConstant: CGFloat = 0, bottomConstant: CGFloat = 0, rightConstant: CGFloat = 0, widthConstant: CGFloat = 0, heightConstant: CGFloat = 0) -> [NSLayoutConstraint] {
                translatesAutoresizingMaskIntoConstraints = false

                var anchors = [NSLayoutConstraint]()

                if let top = top {
                    anchors.append(topAnchor.constraint(equalTo: top, constant: topConstant))
                }

                if let left = left {
                    anchors.append(leftAnchor.constraint(equalTo: left, constant: leftConstant))
                }

                if let bottom = bottom {
                    anchors.append(bottomAnchor.constraint(equalTo: bottom, constant: -bottomConstant))
                }

                if let right = right {
                    anchors.append(rightAnchor.constraint(equalTo: right, constant: -rightConstant))
                }

                if widthConstant > 0 {
                    anchors.append(widthAnchor.constraint(equalToConstant: widthConstant))
                }

                if heightConstant > 0 {
                    anchors.append(heightAnchor.constraint(equalToConstant: heightConstant))
                }

                anchors.forEach({$0.isActive = true})

                return anchors
                         }

                  }
                  
          Usage of this 

                  override init(frame: CGRect) {
                  super.init(frame: frame)

                  addSubview(logoImageView)
                  addSubview(emailTextField)
                  addSubview(passwordTextField)
                  addSubview(loginButton)

                  _ = logoImageView.anchor(centerYAnchor, left: nil, bottom: nil, right: nil, topConstant: -230, leftConstant: 0, bottomConstant: 0, rightConstant: 0, widthConstant: 160, heightConstant: 160)
                  logoImageView.centerXAnchor.constraint(equalTo: centerXAnchor).isActive = true

                  _ = emailTextField.anchor(logoImageView.bottomAnchor, left: leftAnchor, bottom: nil, right: rightAnchor, topConstant: 8, leftConstant: 32, bottomConstant: 0, rightConstant: 32, widthConstant: 0, heightConstant: 50)

                  _ = passwordTextField.anchor(emailTextField.bottomAnchor, left: leftAnchor, bottom: nil, right: rightAnchor, topConstant: 16, leftConstant: 32, bottomConstant: 0, rightConstant: 32, widthConstant: 0, heightConstant: 50)

                  _ = loginButton.anchor(passwordTextField.bottomAnchor, left: leftAnchor, bottom: nil, right: rightAnchor, topConstant: 16, leftConstant: 32, bottomConstant: 0, rightConstant: 32, widthConstant: 0, heightConstant: 50)
                        }

(2) Padding given to textfield Using its Two methos and use that Inherited class for TextField View
          
          
          
            class LeftPaddedTextField: UITextField {
    
             override func textRect(forBounds bounds: CGRect) -> CGRect {
               return CGRect(x: bounds.origin.x + 10, y: bounds.origin.y, width: bounds.width + 10, height: bounds.height)
             }

             override func editingRect(forBounds bounds: CGRect) -> CGRect {
              return CGRect(x: bounds.origin.x + 10, y: bounds.origin.y, width: bounds.width + 10, height: bounds.height)
             }
    
            }
      
            Usage of this class
        
            let emailTextField: LeftPaddedTextField = {
                  let textField = LeftPaddedTextField()
                  textField.placeholder = "Enter email"
                  textField.layer.borderColor = UIColor.lightGray.cgColor
                  textField.layer.borderWidth = 1
                  textField.keyboardType = .emailAddress
                  return textField
            }()
        
(3) SlidShow Of 4 pages are created using collectionview 

         - on 4th page login screen created 
         - some animation is given (hiding constarint out of screen using some setting frame logic using core animation methods)
         - when scrollview of collectionview moves from 4th page to 3rd page keyboard hide automatically using this method

              func scrollViewDidScroll(_ scrollView: UIScrollView) 
               {
                 view.endEditing(true)
               }

         - page Control is also used on ViewController

               some logic is implemented using scrollview method
                 func scrollViewWillEndDragging()

         - when screen orientation is changed using collectionview method        

                collectionView.collectionViewLayout.invalidateLayout()
                this draw constraints
            

(4) NSUserDefault used for storing login credential
     
            import Foundation
            extension UserDefaults {
                enum UserDefaultsKeys: String {
                    case isLoggedIn
                }
                func setIsLoggedIn(value: Bool) {
                    set(value, forKey: UserDefaultsKeys.isLoggedIn.rawValue)
                    synchronize()
                }
                func isLoggedIn() -> Bool {
                    return bool(forKey: UserDefaultsKeys.isLoggedIn.rawValue)
                }
             }

 (5) highject Slider View Controller using one Navigation Controller
     
            import UIKit

            class MainNavigationController: UINavigationController {
                override func viewDidLoad() {
                    super.viewDidLoad()
                    view.backgroundColor = .white

                    if isLoggedIn() {
                        //assume user is logged in
                        let homeController = HomeController()
                        viewControllers = [homeController]
                    } else {
                        perform(#selector(showLoginController), with: nil, afterDelay: 0.01)
                    }
                }

                fileprivate func isLoggedIn() -> Bool {
                    return UserDefaults.standard.isLoggedIn()
                }

                func showLoginController() {
                    let loginController = LoginController()
                    present(loginController, animated: true, completion: {
                        //perhaps we'll do something here later
                    })
                }
            }
