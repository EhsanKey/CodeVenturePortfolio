👋 سلام دوستان! 

تا حالا به این فکر کردید چطوری میتونیم کامپوننت `Avatar` کتابخونه `Material Tailwind` رو بهینه کنیم؟

```jsx
import { Avatar } from '@material-tailwind/react'
import React from 'react'

const Component = () => {
  return <Avatar src="/logo.jpg" alt="logo" />
}

export default Component
```

داخل کد بالا، ما هیچ بهینه‌سازی نمیتونیم انجام بدیم، چون `Avatar` یک کامپوننت از قبل نوشته شده هستش. اما میتونیم خود کامپوننت `Avatar` رو بهینه کنیم!

### چطوری؟

ما باید کامپوننت `Avatar` رو مجدداً بنویسیم و کامپوننت `Image` از `next.js` رو جایگزین تگ `img` استفاده شده داخل کامپوننت `Avatar` کنیم. داخل کد زیر، این کار رو انجام دادیم:

```jsx
import React from "react";
import Image, { type ImageProps } from "next/image";

// utils
import classnames from "classnames";
import { twMerge } from "tailwind-merge";
import findMatch from "@material-tailwind/react/utils/findMatch";
import objectsToString from "@material-tailwind/react/utils/objectsToString";

// context
import { useTheme } from "@material-tailwind/react/context/theme";

// types
import type {
  variant,
  size,
  className,
  withBorder,
  color,
} from "@material-tailwind/react/types/components/avatar";

type AvatarOwnProps = {
  variant?: variant;
  size?: size;
  className?: className;
  withBorder?: withBorder;
  color?: color;
};

export type AvatarProps = AvatarOwnProps &
  Omit<ImageProps, keyof AvatarOwnProps>;

export const Avatar = React.forwardRef<
  HTMLImageElement,
  React.PropsWithoutRef<AvatarProps>
>(({ variant, size, className, color, withBorder, ...rest }, ref) => {
  // 1. init
  const { avatar } = useTheme();
  const { valid, defaultProps, styles } = avatar;
  const { base, variants, sizes, borderColor } = styles;

  // 2. set default props
  variant = variant ?? defaultProps.variant;
  size = size ?? defaultProps.size;
  className = className ?? defaultProps.className;
  withBorder = withBorder ?? defaultProps.withBorder;
  color = color ?? defaultProps.color;

  // 3. set styles
  const avatarVariant = objectsToString(
    variants[findMatch(valid.variants, variant, "rounded")],
  );
  const avatarSize = objectsToString(sizes[findMatch(valid.sizes, size, "md")]);
  const avatarBorderColor = objectsToString(
    borderColor[findMatch(valid.colors, color, "gray")],
  );
  const classes = twMerge(
    classnames(objectsToString(base.initial), avatarVariant, avatarSize, {
      [objectsToString(base.withBorder)]: withBorder,
      [avatarBorderColor]: withBorder,
    }),
    className,
  );

  // 4. return
  return <Image {...rest} ref={ref} className={classes} />;
});
Avatar.displayName = "MaterialTailwind.Avatar";

export default Avatar;
```

### حالا برای استفاده از این کامپوننت

```jsx
import { Avatar } from '@/components/ui/avatar'
import React from 'react'

const Component = () => {
  return <Avatar src="/logo.jpg" alt="logo" />
}

export default Component;
```

به همین راحتی 🍰!
