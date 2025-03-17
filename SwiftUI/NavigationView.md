

# 2025.03.17
# NavigationView

## NavigationView 적용해보기
```swift

//...

private var phoneLoginButton : some View {
        NavigationLink {
            PhoneLoginView(viewModel: PhoneLoginViewModel(repository: PhoneLoginRepository()))
        } label: {
            Text("핸드폰 번호로 로그인하기")
                .frame(maxHeight: 24)
                .foregroundStyle(.black)
                .font(.subheadline)
                .underline(color: .gray)
                .bold()
        }
}

var body: some View {    
    NavigationView {
        VStack {
            phoneLoginButton
        }
    }
}

//...
```

- 개인 프로젝트 내 LoginHubView의 body 내에 UI를 NavigationView로 Embed 한다.
- NavigationLink를 통해 destination이 될 View를 나타내고, 해당 버튼의 UI를 label 부분에 구현한다.

```swift
// PhoneLoginView
//...

    @Environment(\.dismiss) private var onTapPop

    private var backButton : some View {
        Button {
            onTapPop()
        } label: {
            Image(systemName: "chevron.backward")
                .font(.system(size: 20, weight: .bold, design: .default))
                .foregroundStyle(.black)
        }
        .frame(maxWidth: 20, maxHeight: 20)
    }

    var body : some View {
        VStack {
            
            HStack {
                Text("핸드폰 번호를\n입력해주세요.")
                    .font(.title)
                    .bold()
                Spacer()
            }
            .padding(.top, 10)
            
            Spacer()
                .frame(height: 14)
            
            phoneNumberField
            
            Spacer()
            
            requestButton
        }
        .padding()
        .navigationBarBackButtonHidden()
        .toolbar {
            ToolbarItem(placement: .navigationBarLeading)
            {
                backButton
            }
        }
    }
//...
```
- LoginHubView의 destination이 될 PhoneLoginView의 가장 상위의 VStack에 _.toolbar_ modifier에 이미지로 나타내는 backButton을 ToolbarItem으로 .navigationBarLeading 왼쪽 상단에 위치시킨다.
- @Environment onTapPop을 사용해서, 해당 버튼 클릭 시 이전 화면으로 이동할 수 있도록 적용했다.