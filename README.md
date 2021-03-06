# MVVM  

<img src="https://github.com/YamamotoDesu/MVVM/blob/main/MVVM.playground/Resources/MVVM_Diagram.png" width="600" height="300">
The Model-View-ViewModel (MVVM) pattern separates objects into three types: models, views and view-models.

Models hold onto application data. They are usually structs or simple classes.  
View-models convert models into a format that views can use.  
Views display visual elements and controls on screen. They are usually subclasses of UIView.  

## Code Example(MVVM)
```swift
// MARK: - Model
public class Pet {
    public enum Rarity {
        case common
        case uncommon
        case rare
        case veryRare
    }
    public let name: String
    public let birthday: Date
    public let rarity: Rarity
    public let image: UIImage
    
    public init(name: String,
                birthday: Date,
                rarity: Rarity,
                image: UIImage) {
        self.name = name
        self.birthday = birthday
        self.rarity = rarity
        self.image = image
    }
}

// MARK: - ViewModel
public class PetViewModel {
    private let pet: Pet
    private let calendar: Calendar
    
    public init(pet: Pet,
                calendar: Calendar = Calendar(identifier: .gregorian)) {
        self.pet = pet
        self.calendar = calendar
    }
    
    public var name: String {
        return pet.name
    }
    
    public var image: UIImage {
        return pet.image
    }
    
    public var ageText: String {
        let today = calendar.startOfDay(for: Date())
        let birthday = calendar.startOfDay(for: pet.birthday)
        let components = calendar.dateComponents([.year], from: birthday, to: today)
        let age = components.year!
        return "\(age) years old"
    }
    
    public var adoptionFee: String {
        switch pet.rarity {
        case .common:
            return "$50.00"
        case .uncommon:
            return "$75.00"
        case .rare:
            return "$150.00"
        case .veryRare:
            return "$500.00"
        }
    }
}

// MARK: - View & ViewController
public class PetView: UIView {
    public let imageView: UIImageView
    public let nameLabel: UILabel
    public let ageLabel: UILabel
    public let adoptionFeeLabel: UILabel
    
    public override init(frame: CGRect) {
        var childFrame = CGRect(x: 0.0, y: 16.0,
                                width: frame.width, height: frame.height / 2)
        imageView = UIImageView(frame: childFrame)
        imageView.contentMode = .scaleAspectFit
        
        childFrame.origin.y += childFrame.height + 16.0
        childFrame.size.height = 30.0
        nameLabel = UILabel(frame: childFrame)
        nameLabel.textAlignment = .center
        
        childFrame.origin.y += childFrame.height
        ageLabel = UILabel(frame: childFrame)
        ageLabel.textAlignment = .center
        
        childFrame.origin.y += childFrame.height
        adoptionFeeLabel = UILabel(frame: childFrame)
        adoptionFeeLabel.textAlignment = .center
        
        super.init(frame: frame)
        backgroundColor = .white
        addSubview(imageView)
        addSubview(nameLabel)
        addSubview(ageLabel)
        addSubview(adoptionFeeLabel)
    }
    
    @available(*, unavailable)
    public required init?(coder: NSCoder) {
        fatalError("Use init(frame:) instead")
    }
}

extension PetViewModel {
    public func configure(_ view: PetView) {
        view.nameLabel.text = name
        view.imageView.image = image
        view.ageLabel.text = ageText
        view.adoptionFeeLabel.text = adoptionFee
    }
}

// MARK: - Example
let birthday = Date(timeIntervalSinceNow: -2 * 86400 * 366)
let image = UIImage(named: "stuart")!
let stuart = Pet(name: "Stuart",
                 birthday: birthday,
                 rarity: .veryRare,
                 image: image)
let viewModel = PetViewModel(pet: stuart)

let frame = CGRect(x: 0.0, y: 0.0, width: 300.0, height: 420.0)
let view = PetView(frame: frame)
viewModel.configure(view)

```

## Compare with Code Example(MVC)
```swift
import PlaygroundSupport
import UIKit

// MARK: - Model
public class Pet {
    public enum Rarity {
        case common
        case uncommon
        case rare
        case veryRare
    }
    public let name: String
    public let birthday: Date
    public let rarity: Rarity
    public let image: UIImage
    
    public init(name: String,
                birthday: Date,
                rarity: Rarity,
                image: UIImage) {
        self.name = name
        self.birthday = birthday
        self.rarity = rarity
        self.image = image
    }
    
    public var adoptionFee: String {
        switch self.rarity {
        case .common:
            return "$50.00"
        case .uncommon:
            return "$75.00"
        case .rare:
            return "$150.00"
        case .veryRare:
            return "$500.00"
        }
    }
}

// MARK: - View & Controller
public class PetView: UIView {

    private let calendar: Calendar = Calendar(identifier: .gregorian)
    
    public let imageView: UIImageView
    public let nameLabel: UILabel
    public var ageLabel: UILabel
    public let adoptionFeeLabel: UILabel
    
    public override init(frame: CGRect) {
        
        var childFrame = CGRect(x: 0.0, y: 16.0,
                                width: frame.width, height: frame.height / 2)
        imageView = UIImageView(frame: childFrame)
        imageView.contentMode = .scaleAspectFit
        
        childFrame.origin.y += childFrame.height + 16.0
        childFrame.size.height = 30.0
        nameLabel = UILabel(frame: childFrame)
        nameLabel.textAlignment = .center
        
        childFrame.origin.y += childFrame.height
        ageLabel = UILabel(frame: childFrame)
        ageLabel.textAlignment = .center
        
        childFrame.origin.y += childFrame.height
        adoptionFeeLabel = UILabel(frame: childFrame)
        adoptionFeeLabel.textAlignment = .center
        super.init(frame: frame)
        
        backgroundColor = .white
        addSubview(imageView)
        addSubview(nameLabel)
        addSubview(ageLabel)
        addSubview(adoptionFeeLabel)
    }
    
    func configure(_ pet: Pet) {
        self.nameLabel.text = pet.name
        self.imageView.image = pet.image
        self.ageLabel.text = getAge(pet.birthday)
        self.adoptionFeeLabel.text = pet.adoptionFee
    }
    
    func getAge(_ birthday: Date) -> String {
        let today = calendar.startOfDay(for: Date())
        let birthday = calendar.startOfDay(for: birthday)
        let components = calendar.dateComponents([.year], from: birthday, to: today)
        let age = components.year!
        return  "\(age) years old"
    }
    
    @available(*, unavailable)
    public required init?(coder: NSCoder) {
        fatalError("Use init(frame:) instead")
    }
}

// MARK: - Example
let birthday = Date(timeIntervalSinceNow: -2 * 86400 * 366)
let image = UIImage(named: "stuart")!
let stuart = Pet(name: "Stuart",
                 birthday: birthday,
                 rarity: .veryRare,
                 image: image)

let frame = CGRect(x: 0.0, y: 0.0, width: 300.0, height: 420.0)
let view = PetView(frame: frame)
view.configure(stuart)

PlaygroundPage.current.liveView = view

```
