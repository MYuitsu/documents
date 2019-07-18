## Default Props React Typescript 

```javascripts
    export interface Props {
    name: string;
    }

    export class Greet extends React.Component<Props> {
        render() {
            const { name } = this.props;
            return <div>Hello ${name.toUpperCase()}!</div>;
        }
        static defaultProps = { name: "world"};
    }

```

